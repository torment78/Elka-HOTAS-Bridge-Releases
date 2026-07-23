# Migration Plan

This document records the Chapter 1 baseline and the agreed migration approach for HOTASBridge Version 2.

## Chapter 1 Scope

Chapter 1 is an assessment and documentation milestone. No subsystem rewrite is allowed in this chapter.

Added during this pass by direct Agent Note 001: a narrow Debug-only Developer Dashboard. It is diagnostics-only and does not alter Release behavior or runtime mapping behavior.

## Baseline Validation

| Validation Item | Result | Notes |
| --- | --- | --- |
| Git status before work | Clean | Latest baseline commit was `63a7a6d Bundle ViGEmBus first-run installer`. |
| Debug build | Passed | 0 warnings, 0 errors. |
| Release build | Passed | 0 warnings, 0 errors. |
| Automated tests | Passed | 19 passed. |
| Application smoke launch | Partial pass | Process started and logged `ApplicationStartup`; ViGEmBus missing caused expected first-run prompt, so full navigation was not automated. |
| Driver detection | Confirmed missing on this machine | No `ViGEmBus` service and no default driver file path. |
| Bundled driver package | Previously verified | Official ViGEmBus 1.22.0 EXE is copied to app output and signature was valid when bundled. |

## Current Behavior Baseline

| Area | Current Behavior |
| --- | --- |
| Application startup | Creates app paths/log, prompts to install bundled ViGEmBus if the driver is missing, then manually composes services and opens `MainWindow`. |
| Profile loading | Loads JSON profiles from `%LOCALAPPDATA%\HOTASBridge\Profiles`; creates a default simulation profile when none exist. |
| Device discovery | Combines HID, Raw Input, and simulated devices, then filters demo devices out unless enabled. |
| Profile device selection | User selects visible devices and adds them to the active profile. |
| Device inspector | Creates one tab for each enabled selected profile device that is currently visible/detected. |
| Input runtime | Starts HID and simulation event sources for selected device IDs. HID readers run background tasks; events are forwarded to the UI dispatcher. |
| Mapping execution | `MappingEngine.ApplyEvent` applies matching enabled mappings to the current `XboxState`. |
| Output updates | Current Xbox state is sent to `IVirtualGamepadOutput`; ViGEm creates a system-visible Xbox 360 controller only when ViGEmBus is installed. |
| Stop mapping | Cancels input, stops event sources, neutralizes output, disconnects virtual controller, updates UI. |
| Profile saving | Validates and atomically saves schema v3 JSON, updates timestamps, and increments the profile revision. |
| Logging | Writes JSON-line events to the local app data log directory. |

## Migration Principles

1. Preserve working input, mapping, output, profile, and UI behavior.
2. Add regression protection before major refactoring.
3. Move toward `RuntimeSignal -> Mapping/Transform -> OutputAction` incrementally.
4. Keep driver-specific output code isolated.
5. Introduce service boundaries before plugin loading.
6. Avoid long-lived unstable states.

## Chapter 2 Additions

Chapter 2 added the core architecture direction without replacing working subsystems:

- `RuntimeSignal` is now the normalized runtime model and mapping input.
- `IRuntimeTelemetry` is now the shared telemetry API for dashboard, diagnostics exporter, and future profiler.
- `RuntimeStageDiagnostic` is now the shared diagnostic metadata model for future Signal Flow Inspector.
- `IMappingEngine` is now an interface boundary instead of a UI-owned concrete dependency.

## Chapter 3 Additions

Chapter 3 turns the RuntimeSignal foundation into the active input and mapping processing path:

- `RuntimeSignalEngine` owns conversion, validation, immutable publication, and latest-value caching.
- UI, learning, diagnostics, and mapping consume the same published signal.
- `IRuntimeSignalPipeline` provides an ordered stage extension contract.
- Existing axis behavior is represented by signal stages and protected by equivalence tests.
- Mapping runtime state is separate from profile configuration; Output Mapping emits a virtual signal before the XboxState adapter.

## Chapter 4 Additions

Chapter 4 moves all app-facing input behavior behind `IInputProvider`:

- Existing HID, Raw Input, and simulation code is preserved behind per-provider adapters; the unused pre-provider composite catalog/event source have been retired.
- Provider adapters own RuntimeSignal conversion.
- A composite manager owns discovery deduplication, lifecycle monitoring, health, errors, and telemetry.
- `MainViewModel` no longer references catalogs, event sources, or the RuntimeSignal engine directly.
- Additive identity fields preserve existing profile JSON and Stable IDs.
- Learn Mode is a confirm/retry session and can capture without starting Xbox output.

DirectInput remains explicit future work. Native Windows HID notifications wake the existing provider discovery path with polling fallback, and SetupAPI Container IDs now strengthen profile reconnection without changing existing Stable IDs or requiring a schema migration.

## Chapter 5 Additions

Chapter 5 keeps diagnostics read-only and additive:

- Device Inspector values are resolved from the Runtime Signal Cache, never hardware providers.
- Runtime stage diagnostics add source/control, pipeline, and order fields; existing consumers remain compatible.
- Diagnostics exports use new Core DTOs and do not change profile or mapping JSON.
- Signal Flow Inspector consumes the shared cache and telemetry service.
- No user profile migration is required.

## Chapter 6 Additions

Chapter 6 upgrades persistence without replacing the working mapping format:

- Supported v1 documents migrate to schema v2 after an exact source backup.
- Existing `axisProcessing`, `buttonProcessing`, behavior, curves, layers, and output target remain compatible.
- Additive metadata, device groups, output plugin configuration, and transform descriptors prepare future plugin/runtime work.
- Unknown extension transform nodes survive compatibility synchronization.
- Profiles with newer schemas are not downgraded and are blocked from save.
- Application settings, profiles, backups, templates, logs, diagnostics, and temporary files use separate locations.
- No manual user migration is required.

## Chapter 7 Additions

Chapter 7 replaces the live Xbox-state mapping boundary without replacing working behavior:

- v1/v2 profiles migrate to schema v3 after an exact backup.
- The temporary `ApplyEvent`/`ApplySignal` regression adapters were retired after all callers migrated to `ProcessSignal` and OutputAction reduction.
- The app uses indexed mappings and Output Manager action dispatch.
- Runtime contributions, priorities, conditions, and activity stay outside profiles.
- Known axis transform descriptors execute in stored order; descriptor-free mappings use the legacy order.
- Live edits swap mapping snapshots and emit release/transfer actions.
- No manual user migration is required.

## Version 2 Incremental Sequence

| Step | Goal | Notes |
| --- | --- | --- |
| 1 | Current assessment and documentation | This chapter. |
| 2 | Core architecture and diagnostics foundation | RuntimeSignal, telemetry service, stage diagnostics, and architecture documentation completed as a foundation. |
| 3 | Dependency injection cleanup | Replace manual construction with explicit registrations. |
| 4 | RuntimeSignal expansion | Input publication, pipeline, runtime state, and latest-value cache complete; output actions, recording/playback, and plugin SDK remain. |
| 5 | Scheduler/runtime coordinator | Move mapping loop off UI dispatcher path. |
| 6 | OutputAction/output manager | Completed foundation in Chapter 7; Xbox backend active, dynamic plugins deferred. |
| 7 | Hardware validation expansion | Compatibility matrix and device-specific test logs. |
| 8 | UI modularization | Split pages after smoke/regression protection exists. |

## Deferred Work

- Keyboard/mouse output.
- Recording/playback.
- AI-assisted signal diagnostics.
- Formal plugin marketplace/loading.
- Major UI redesign.

## Acceptance Criteria for Future Chapters

Each chapter must:

- compare the chapter against this baseline;
- build Debug;
- build Release;
- run relevant tests;
- update `CURRENT_FEATURE_MATRIX.md`;
- update `TECHNICAL_DEBT.md` when items are opened/closed;
- commit one logical milestone.

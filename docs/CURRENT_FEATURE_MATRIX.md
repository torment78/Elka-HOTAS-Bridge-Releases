# Current Feature Matrix

This document describes the current HOTASBridge implementation as of Chapter 25, Living Architecture and Governance foundation.

## Requirement Classification

| Chapter 1 Requirement | Status | Notes |
| --- | --- | --- |
| Current architecture documented | Complete | See `docs/CURRENT_ARCHITECTURE.md`. |
| Current feature matrix created | Complete | This document is the living feature matrix. |
| Current behavior documented | Complete | See `docs/MIGRATION_PLAN.md` baseline behavior section. |
| Technical debt documented | Complete | See `docs/TECHNICAL_DEBT.md`. |
| Existing build verified | Complete | Debug and Release builds passed with zero warnings. |
| Existing functionality protected | Complete automated foundation | 505 automated tests protect core, deployment policy, feature policy, AI evidence/approval policy, project health, architecture rules, runtime stores and mapping coordination, Windows input/driver boundaries, graph and macro editing, signal engine/cache/pipeline, input providers/lifecycle/learn mode, device coordination, identity reconciliation, telemetry, persistence, simulation, recording/playback, mapping/output behavior, guided presets, Easy Mode axis curves, stopped-runtime previews, process-aware profile activation, plugin compatibility/discovery, scripting isolation, and output neutralization; live hardware validation remains a separate manual gate. |
| Migration strategy agreed/documented | Complete | See `docs/MIGRATION_PLAN.md`. |
| Application dependency injection | Complete foundation | Microsoft DI registers the application service graph in a dedicated composition root; startup and shutdown ordering remain explicit. |
| Developer Dashboard, Agent Note 001 | Complete | Debug-only diagnostics page enabled through Debug navigation only. |
| Core architecture, Chapter 2 | Complete foundation | `docs/ARCHITECTURE.md` now defines runtime flow, thread ownership, dependencies, service registration, startup, and shutdown. |
| Telemetry framework, Agent Note 002 | Complete foundation | `IRuntimeTelemetry` and `InMemoryRuntimeTelemetry` added; Developer Dashboard consumes telemetry snapshots. |
| Stage diagnostics, Agent Note 003 | Complete foundation | Runtime stages expose current values, enabled state, timestamps, duration, warning/error state, and descriptions. |
| RuntimeSignal model, Agent Note 004 | Complete foundation | `RuntimeSignal` added and mapping engine consumes signals through `IMappingEngine`. |
| Runtime Signal Engine, Chapter 3 | Complete | Input publication, immutable snapshots, ordered stages, quality/error handling, and runtime state are active. |
| Runtime Signal Cache, Agent Note 004 | Complete | Engine-owned latest-value cache exposes read-only lookup and snapshots. |
| Signal-native output plugins | Complete foundation | OutputAction batches route through independent Xbox, Keyboard, and Mouse plugins; ViGEm Xbox 360 remains the stable default and feature-gated HIDMaestro Xbox One is Beta. |
| Input Device Layer, Chapter 4 | Complete foundation | Common provider manager plus discovery/profile-device and input-monitoring coordinators are active while native HID code is preserved. |
| RuntimeSignal input publication | Complete | Input DTO conversion is internal to provider adapters. |
| Automatic device lifecycle | Complete | Native HID topology notifications trigger debounced discovery and connected/disconnected/reconnected events; 30-second safety polling and automatic two-second degraded fallback remain active. |
| DirectInput provider | Deferred | No backend or dependency exists yet. |
| Device Inspector diagnostics, Chapter 5 | Complete | Cache-backed summary, live controls, performance, mapping preview, filters, freeze/reset, and exports. |
| Signal Flow Inspector, Agent Note 006 | Complete foundation | Live/freeze view consumes shared cache and source-filtered telemetry stages. |
| Profile system, Chapter 6 | Complete | Versioned migration backup, validation, workflows, templates, recents, change-aware Auto Save, and separated storage. |
| Profile Health Report, Agent Note 007 | Complete | Automatic status and actionable issue navigation without profile mutation. |
| Mapping Engine, Chapter 7 | Complete | Indexed action pipeline, conditions, ordered transforms, runtime contributions, conflicts, and live rebuilds. |
| Runtime mapping coordination | Complete foundation | Core coordinator owns context/held-state snapshots, deterministic RuntimeSignal scheduling, OutputAction dispatch, queue rejection metrics, drain, and reset; WPF retains presentation plus macro/script hooks. |
| Mapping Explorer, Agent Note 008 | Complete | Search, grouping, sorting, multi-select, bulk actions, export, runtime state, and editor navigation. |
| Plugin SDK, Chapter 15 | Complete foundation | Shared manifest/catalog/version/dependency/lifecycle contracts plus startup metadata discovery. |
| Online-ready Plugin Manager, Agent Note 016 | Complete architecture | Installed search and repository/package/signature extension points; online services and package mutation are deferred. |
| Roadmap/version/release policy, Chapter 16 | Complete foundation | Central 0.x versioning, release gates, and UI-independent maturity-aware feature flags. |
| Final validation and Project Health, Chapter 17 | Complete foundation | Machine-readable readiness, merged coverage, automated release validation, and explicit manual blockers. |
| Visual Node Editor, Chapter 18 | Complete branching-graph foundation | Shared mapping model, optional schema-v9 graph, typed direct ports, logic/internal plugin nodes, live diagnostics, workspace layouts, virtualization, history, and Beta boundary. |
| Macro Engine, Chapter 19 | Complete foundation | RuntimeSignal/system triggers, conditions, actions, central scheduling, variables, safety cleanup, diagnostics, and Beta debugger. |
| Scripting Engine, Chapter 20 | Complete trusted foundation | Optional Lua runtime, stable Script API, signed packages, local publisher trust, explicit permissions, bounded resources, fail-closed isolation policy, diagnostics, and Script Workbench. |
| Profile Library, Chapter 21 | Complete local foundation | Searchable metadata, package providers, compatibility preview, templates, comparison, merge, and reports; online services are deferred. |
| Installer and deployment, Chapter 22 | Complete signed release-engineering foundation | Certificate-backed application/setup/uninstaller signing, SHA-256 manifests, independent verification, backup/rollback, and disposable-machine acceptance tooling are active; clean-machine evidence remains a release gate. |
| First Run Wizard, Agent Note 023 | Complete | Skippable nine-step setup, selected-device input testing, optional starter mapping, explicit driver install, and Xbox output verification. |
| Coding standards and Architecture Validator, Chapter 23 | Complete foundation | Versioned policy, hard dependency/WPF/solution gates, incremental source heuristics, contributor guidance, ADR process, release integration, and a current baseline with no findings. |
| AI Assistant, Chapter 24 | Complete local foundation | Offline read-only explanations consume bounded current evidence at five UI entry points; remote models, generation, learning, and change application are deferred. |
| Long-Term Vision and Governance, Chapter 25 | Complete foundation | Living vision, governance policy, accepted ADRs, formal review records, and an executable milestone schedule preserve the runtime while gating compatibility-expanding work. |

## Feature Inventory

| Feature | Status | Protection | Notes |
| --- | --- | --- | --- |
| Solution build | Complete | Debug/Release build | .NET 10 solution builds cleanly. |
| Product versioning | Complete foundation | Build metadata/test/manual | Central `0.26.0-dev` version, source revision, and validated Development/Beta/Stable channels. |
| WPF application shell | Complete | Build/UI Automation/visual | Preserves existing pages inside a themed workspace shell with global commands and status. |
| Left navigation pages | Complete | UI Automation | Consistent task order and labels with stable keys, advanced pages, Debug-only Developer Dashboard, Performance Profiler, and Test Runner. |
| Dockable workspace | Complete foundation | Integration/UI Automation/visual | Resizable left/right/bottom panes, move, hide/show, pin/unpin, named/custom layouts, four built-ins, and independent JSON persistence; floating windows deferred. |
| UI themes | Complete foundation | Build/visual/settings test | Light, Dark, and Windows System modes use dynamic resources and persisted application settings. |
| UI status and notifications | Complete foundation | UI Automation | Persistent status bar plus bounded recent activity over unobtrusive shell messages. |
| Physical HID discovery | Complete | Integration/manual/hardware | Uses SetupAPI/HID APIs through `WindowsHidDeviceProvider`; native notifications, polling fallback, and GUID Container ID retrieval have direct Windows-boundary coverage. |
| Raw Input discovery foundation | Complete | Manual | Present through `WindowsRawInputDeviceProvider`; HID provider is the stronger active discovery path. |
| Physical HID input reading | Complete | Manual/hardware | `WindowsHidInputService` reads selected HID reports in background tasks. |
| Simulated devices | Complete | Integration tests | Five built-in scenarios support mixed, seeded-random, and scripted input while preserving the Generic HOTAS default. |
| Input source filtering | Complete | Manual | Physical, virtual, both modes exist. |
| Provider deduplication and health | Complete | Integration tests | Strong same-interface representations correlate with live/control precedence, warnings, telemetry, and an Advanced override; distinct virtual paths remain independent. |
| Duplicate input warnings | Complete | Unit-level coverage through related domain tests | `DuplicateInputDetector` warns on likely physical/virtual duplicate paths. |
| Per-device inspector tabs | Complete | Manual | Selected profile devices receive individual tabs. |
| Live axis display | Complete | Manual/hardware/simulation | Axis bars and X/Y visualizer update from RuntimeSignals. |
| Button display beyond 32 buttons | Complete | Integration test via simulation | Simulated button box exceeds 32 buttons; HID caps allow up to 256 displayed buttons. |
| Hat/POV display and mapping | Complete provider-contract foundation | Core/integration tests/manual hardware pending | Provider-aware normalization, raw diagnostics, visual direction, center press, diagonal policy, PlayStation-style D-pad, and Xbox/keyboard/mouse routing. |
| Encoders and switches | Partial | Manual | Model/UI placeholders exist; hardware-specific detection remains limited. |
| Learn input | Complete | Unit/manual | Selected device/group scope, baseline noise rejection, first meaningful input, inspector highlight, values, Confirm, and Retry. |
| Mapping editor | Complete | Unit/integration tests | Visual input picker plus Xbox, keyboard, and mouse target surfaces create mappings; eligible axis-to-keyboard mappings include guided Analog PWM presets and validation. |
| Visual Node Editor | Complete branching-graph foundation | Core/integration tests/Debug/Release/WPF smoke | Optional persisted DAGs edit the same InputMapping, with typed direct ports, deterministic logic, registered internal plugin nodes, validation, history, workspace positions, minimap, viewport culling, and live diagnostics. |
| Macro Engine and Editor | Complete Beta foundation | Core/integration/build/startup smoke | Profile macros use RuntimeSignals, OutputActions, the central scheduler, runtime-only variables, safeguards, shared diagnostics, visual authoring over the same model, and a live debugger; libraries/import/export remain deferred. |
| Lua Scripting Engine | Complete Experimental trusted foundation | Dedicated scripting tests/Debug/WPF smoke | Local and signature-trusted scripts use versioned services, explicit permissions, bounded execution, signed packages, atomic Workbench editing, and isolated diagnostics; untrusted execution remains blocked pending a process host. |
| Axis-to-Xbox-axis mapping | Complete | Unit/integration tests | Protected by mapping and axis tests. |
| Button-to-Xbox-button mapping | Complete | Unit tests | Momentary and toggle covered. |
| Axis curves | Complete foundation | Unit tests | Linear, exponential, S-curve, custom model; custom point mouse editing deferred. |
| Profile JSON load/save | Complete | Unit/integration tests | Human-readable, validated, atomic schema v9 JSON with optional branching graphs in the local Profiles directory. |
| Profile migration | Complete | Unit/integration tests | v1-v8 upgrade automatically after an exact backup; graph-free mappings remain graph-free and newer schemas are never downgraded. |
| Profile workflows | Complete | Integration tests/manual UI | Import preview, legacy/package Export, Duplicate, Rename, Save As, searchable metadata, templates, comparison/merge, recents, and configurable Auto Save that skips unchanged intervals. |
| Profile Health Report | Complete | Unit tests/manual UI | Missing devices, duplicates, conflicts, plugins, transforms, validation, and migration status. |
| Process-aware profile activation | Complete foundation | Core/integration/build/manual UI | Optional executable or running-process target, local polling, exact-path preference, conflict-safe selection, and Safe Mode suppression. |
| Application settings persistence | Complete foundation | Integration tests | Schema v6 persists interface mode, visual keyboard layout, provider correlation, window placement, and workspace state separately from profiles. |
| Easy/Advanced presentation | Complete foundation | Core/integration/build/manual UI | Easy navigation and presets use the same profiles/runtime; Advanced restores the complete tool surface. |
| Visual keyboard and mouse mapping | Complete foundation | Core/integration/build/manual UI | US ANSI/Nordic data layouts, stable key identity, assignment/live states, visual mouse targets, and accessible list/capture fallbacks. |
| Mouse output | Complete foundation | Core/integration/build/manual OS validation pending | Shared-scheduler relative movement, proportional axes, acceleration, modifiers, five buttons, two wheel axes, diagnostics, and emergency cleanup. |
| TrackIR input | Complete native-provider foundation | Integration/build/manual hardware pending | Dynamically loads the installed NPClient library, validates signatures, receives six-axis frames, normalizes coordinates, detects stale/mouse-emulation states, and cleans up through `IHeadTrackingProvider`; no vendor SDK files are bundled. |
| Feature flags | Complete foundation | Core/integration/Release build | Stable, Beta, Experimental, DebugOnly, and Hidden policy gates real composition/navigation boundaries and preserves disabled configuration. |
| Virtual Xbox 360 output | Complete when driver installed | Integration test for neutralization | ViGEm-backed output creates Xbox 360 controller if ViGEmBus is installed. |
| Bundled driver installer | Complete explicit flow | Build output/integration/smoke | Official ViGEmBus 1.22.0 EXE is bundled; installation requires wizard confirmation and visible UAC. |
| Driver detection | Complete | Core/integration/smoke | Shared service checks ViGEmBus registry/service and default driver paths without blocking startup. |
| Application installer | Complete signed foundation | Inno compile/sign/verify and release publication | Per-user or machine-wide installer detects prerequisites, backs up before upgrade, preserves user data by default, and has produced certificate-backed signed artifacts; clean-machine validation remains open. |
| Update service | Complete offline foundation | Core/integration tests | Stable/Beta channel, explicit confirmation, and signature policy are modeled; network checks/download/install are intentionally disabled. |
| First Run Wizard | Complete | Build/integration/smoke | Existing installs skip automatically; users can reopen setup from About. |
| Output Monitor | Complete | UI Automation | Shows plugin lifecycle/health/rate/queue/errors plus typed Xbox and Keyboard state with reset controls. |
| Structured logging | Complete | Integration/smoke | Bounded asynchronous JSON lines with queue telemetry and deterministic shutdown flush. |
| System tray support | Complete foundation | Manual | Tray service exists; deeper tray workflow validation deferred. |
| Diagnostics | Complete foundation | Core/integration/manual | The text log, Device Inspector exports, Signal Flow Inspector, Output Monitor, Developer Dashboard, Project Health, and Debug-only profiler consume shared runtime diagnostics without reading hardware directly. |
| Developer Dashboard | Complete | Debug/Release build validation | Debug-only navigation page displays build/git data and consumes `IRuntimeTelemetry` for runtime, driver, scheduler, UI, process, memory, and stage metrics. |
| Runtime Performance Profiler | Complete foundation | Debug/Release build and UI validation | Debug-only recording, atomic JSON save/load, and average-metric session comparison; profiler sources are removed from Release. |
| Built-in Test Runner | Complete foundation | Debug/Release build and automated tests | Debug-only six-suite validation, JSON/HTML export, and signal recording/replay controls; sources are removed from Release. |
| Project Health | Complete foundation | Core/integration/UI/release validation | Stable page consumes a validated embedded report and live feature flags; manual gates remain explicit. |
| RuntimeSignals abstraction | Complete | Unit tests | Published signals carry raw/normalized/current/previous values, source, type, state, quality, metadata, diagnostics, history, and flags. |
| Runtime Signal Cache | Complete | Unit tests | Stores the latest immutable signal per active control through an engine-only update path. |
| Runtime signal pipeline | Complete | Unit tests | Ordered insertable stages preserve existing axis behavior and isolate stage failures. |
| Runtime mapping state | Complete | Unit tests | Runtime values, toggles, pulses, transform, and timer state are separate from profile configuration. |
| OutputAction abstraction | Complete foundation | Unit/integration tests | Live mapping emits immutable actions; XboxState is now an Output Manager/backend compatibility state. |
| Runtime work scheduler | Complete foundation | Integration tests | Bounded measured lanes isolate mapping and output dispatch from WPF; drain barriers preserve accepted work at shutdown. |
| Output scheduler | Complete | Integration test | One shared timer loop owns delayed, repeat, PWM, and future timed output jobs with current/average/worst latency telemetry. |
| Output plugins | Complete foundation | Integration tests | Xbox and Keyboard plugins are active with isolated lifecycle/reset/diagnostics and SDK manifests. |
| Plugin catalog/discovery | Complete foundation | Core/integration tests | Startup discovers bounded local manifests, validates API/dependencies/duplicates, and never executes external assemblies. |
| Plugin Manager service | Complete architecture | Core/integration tests | Installed search, compatibility, repository, package, release-note/rating, and signature seams use offline/read-only defaults. |
| Keyboard output | Complete | Core/integration/UI Automation | Captured VK/scan codes, injected-event filtering, combinations, repeat, guided mapping-owned bipolar PWM, diagnostics, reference counting, and reset are active. |
| Recording/playback | Complete foundation | Core/integration tests | Versioned RuntimeSignal JSON, atomic store, timed/deterministic playback, replay/synthetic flags, and OutputAction regression comparison. |

## Hardware Compatibility List

| Device | Status | Notes |
| --- | --- | --- |
| Generic Windows HID joystick/gamepad/multi-axis controller | Supported foundation | Enumerated through HID/SetupAPI when present. |
| WinWing Orion joystick/throttle | User-validated partial | User reported real devices are visible and input is working after HID fixes; formal validation matrix still needed. |
| vJoy input devices | Supported discovery foundation | User-installed vJoy devices are classified as virtual when the HID path/name indicates vJoy; HOTASBridge does not bundle the driver or provide vJoy output. |
| ViGEm virtual Xbox 360 output | Driver-dependent | Works when ViGEmBus is installed; driver was present during Chapter 2 smoke validation. |
| NaturalPoint TrackIR camera | Driver-dependent | Native NPClient integration is implemented; physical camera/software validation and public-release license review remain pending. |
| Simulated joystick/throttle/pedals/button box | Tested | Used by integration tests and demo mode. |
| Thrustmaster, Virpil, VKB, Logitech, Turtle Beach | Not formally validated | Generic HID support should cover many devices; hardware-specific compatibility not yet recorded. |

The canonical status definitions, firmware/driver fields, five simulation entries, and validation checklist are maintained in `docs/HARDWARE_COMPATIBILITY.md`.

## Regression Protection Inventory

| Area | Current Protection |
| --- | --- |
| Axis normalization/deadzone/trigger conversion | `AxisProcessorTests` |
| Curves | `CurveProcessorTests` |
| Mapping engine | `MappingEngineTests`, `RuntimeSignalTests`, `SimulationIntegrationTests` |
| Device identity matching | `DeviceAndProfileTests` |
| Profile serialization/migration/health | `DeviceAndProfileTests`, `ProfileSystemTests`, `ProfilePersistenceTests`, `SimulationIntegrationTests` |
| Simulated device catalog | `SimulationIntegrationTests` |
| Simulation scenarios and scripted input | `TestingSimulationIntegrationTests` |
| RuntimeSignal recording/playback and action regression | `RuntimeSignalRecordingTests`, `TestingSimulationIntegrationTests` |
| Output neutralization/lifecycle/isolation | `SimulationIntegrationTests`, `OutputManagerTests`, `OutputSystemTests` |
| WPF UI navigation/workspace | Isolated UI Automation plus visual review |
| Live HID parsing | Manual/hardware only |
| ViGEm controller creation | Manual/driver-dependent |
| Head-tracking providers | HeadTrackingIntegrationTests, LookPilotFreeTrackHeadTrackingProviderTests, TrackIrHeadTrackingProviderTests; physical camera validation remains manual |

# Elka HOTAS Bridge Release Notes

## 0.29.0 Dev Release 3 - Easy Mode Xbox Family Setup

This is an **unsigned Windows 10/11 x64 development build** that corrects the
Xbox Family Output setup path in Dev 2. Windows may display an Unknown Publisher
warning. It is not the signed Stable release and is deliberately not marked
Latest on GitHub.

Highlights:

- Added a dedicated **Xbox Family Output (Beta)** switch to **Settings > Output**
  in Easy Mode.
- Added the same always-visible enable/restart panel to the top of **Output**, so
  the backend can be discovered even before its plugin and driver are loaded.
- Preserved the developer-only full feature table while exposing only this
  supported Beta opt-in to normal Release users.
- Corrected Xbox-family labels and setup documentation from Experimental and the
  nonexistent Settings > Features path to the actual Beta workflow.

Validation:

- 513 automated tests pass with zero failures.
- Debug and Release builds complete with zero warnings and zero errors.
- The isolated WPF smoke suite confirms the switch is visible in Easy Mode
  Settings > Output and preserves existing Easy, Advanced, and Safe Mode flows.

Enable the switch, close Elka HOTAS Bridge completely, and start it again. Then
open **Output** to install or repair the HIDMaestro driver, choose a controller
identity, and select the Xbox Family output for the active profile.
## 0.29.0 Dev Release 2 - Optional Xbox Family Output

This is an **unsigned Windows 10/11 x64 development build** for testing the
current `dev` branch. Windows may display an Unknown Publisher warning. It is
not the signed Stable release and is deliberately not marked Latest on GitHub.

Highlights:

- Added an optional Beta Xbox-family output backend through HIDMaestro 1.4.3
  while retaining ViGEm Xbox 360 as the default and preserving existing
  `xbox360` mappings without profile migration.
- Added a profile-owned controller identity selector for Xbox 360, Xbox One,
  Xbox One S, Xbox Series X|S, Xbox Elite, and Xbox Adaptive identities.
- Made the ViGEm and HIDMaestro Xbox backends mutually exclusive in both the
  Output Monitor UI and Output Manager runtime safeguards.
- Added explicit HIDMaestro driver installation or repair from Output Monitor.
  Installation always requires confirmation and Windows elevation; the driver
  is never installed silently.
- Added local HIDMaestro license and third-party notices plus hardware-independent
  conversion, routing, profile-validation, and output-selection regressions.

Validation:

- 512 automated tests pass with zero failures.
- Debug and Release builds complete with zero warnings and zero errors.
- The Development installer and portable ZIP are independently verified against
  their generated manifest and SHA-256 checksums.

The installer and ZIP require the **.NET 10 Desktop Runtime**. HIDMaestro is
disabled by default and requires enabling **Xbox Family Output (Beta)** in Settings > Output,
restarting the application, and explicitly installing its driver. Live driver,
Windows controller-panel, and in-game validation remain manual Dev 2 checks.

## 0.29.0 Dev Release 1 - Input, Head Tracking, Curves, and PWM

This is an **unsigned Windows 10/11 x64 development build** for testing the
current `dev` branch. Windows may display an Unknown Publisher warning. It is
not the signed Stable release and is deliberately not marked Latest on GitHub.

Highlights:

- Added native TrackIR input through the installed NaturalPoint NPClient
  interface, alongside OpenTrack UDP and LookPilot UDP/FreeTrack providers.
- Added DualShock 4 and DualSense touchpad input over USB and Bluetooth,
  including contact movement, lift state, two-finger state, and pad click.
- Added profile-owned head-tracking activation, recentering, diagnostics, and
  Absolute Position, Relative Movement, and Velocity mouse modes.
- Added Easy Mode Global and per-key custom Analog PWM authoring with live
  waveform timing and verified keyboard-output scheduling.
- Added precise linked or independent positive/negative curve tuning and
  interactive custom curve points in the Easy axis editor.
- Added repeatable Development-channel packaging that emits an unsigned
  installer, framework-dependent ZIP, release manifest, and SHA-256 checksums.

Validation:

- 505 automated tests pass with zero failed or skipped.
- Debug and Release builds complete with zero warnings and zero errors.
- Architecture validation reports zero errors, warnings, or suggestions.
- The isolated WPF startup/navigation and Safe Mode smoke suite passes.
- Release artifact tooling passes one positive and seven negative safety cases.

The installer and ZIP require the **.NET 10 Desktop Runtime**. ViGEmBus remains
a separate explicit driver installation. NaturalPoint binaries are not bundled;
TrackIR requires the official TrackIR software. Physical TrackIR and clean-machine
acceptance remain manual release checks.

## 0.28.0 Test Release 1 - Mapping Visualization and Presets

This signed Windows 10/11 x64 test release improves the Easy Mode mapping
workflow and lets users verify mappings visually before starting virtual output.

Highlights:

- Grouped Quick Presets for hats, sticks, throttles, buttons, and switches with
  explicit Xbox stick/trigger targets and Direct or Inverted behavior where
  appropriate.
- Added a centered split-axis-to-triggers preset that keeps both Xbox triggers
  neutral at center and scales each physical axis half independently.
- Added isolated stopped-runtime previews for Xbox, keyboard, and mouse outputs;
  the ViGEm virtual controller is still created only when mapping starts.
- Replaced the abstract controller diagram with supplied Xbox 360 top/front line
  art, live stick and D-pad feedback, button highlighting, corrected bumper and
  trigger labeling, leader lines, and analog LT/RT gauges.
- Added a focused Easy Mode Curve Editor from each axis card's right-click menu,
  with live raw/processed values, save/reset controls, and an `S` badge on axes
  that own saved curve settings.
- Enlarged and anchored the controller/output area so Mapping Editor and Output
  Monitor resizing no longer creates a drifting gap or clips the Xbox visual.
- Improved installer repair when existing application binaries are locked while
  retaining user-data backup attempts and Inno Setup rollback protection.

Validation:

- 445 automated tests pass across Core, Integration, Scripting, and Architecture
  Validator suites.
- Debug and Release solution builds complete with zero warnings and zero errors.
- The isolated WPF smoke suite passes fresh/existing setup, Macro Editor, Node
  Editor, Script Workbench, and Safe Mode startup paths.
- Every first-party executable and DLL in the installer and portable package is
  Authenticode-signed and timestamped through the non-GUI SSL.com workflow.

The installer and portable package remain framework-dependent and require the
.NET 10 Desktop Runtime. This remains a test release; back up important profiles
before upgrading.

## 0.27.0 Test Release 1 - Easy Mode Stabilization

This signed Windows 10/11 x64 test release focuses the Release experience on
the stable Easy Mode workflow. Advanced Mode remains available to developers in
Debug builds while its unfinished tools continue under construction.

Highlights:

- A responsive Easy Macro Editor with recording, trigger learning, clear-all
  controls, sustained key and mouse actions, toggles, sequence, and ping-pong
  playback.
- A resizable Saved Mappings area, corrected macro action layout, improved
  mapping and diagnostics responsiveness, and persistent workspace sizing.
- Device-first axis selection, independent negative and positive axis response
  settings, and a stable square curve preview.
- Expanded runtime diagnostics, reliability, release validation, architecture
  checks, and profile compatibility protections.

The installer and portable package are framework-dependent and require the
.NET 10 Desktop Runtime. The installer includes the existing ViGEmBus setup
payload but never installs or removes the driver silently.

Validation evidence and signed-artifact hashes are published with the release.
This remains a test release; back up important profiles before upgrading.

## 0.26.0-dev - Easy Input and Generated Output Workflow

This milestone adds Easy/Advanced presentation over the existing runtime, stable provider-aware hat/D-pad normalization, duplicate-provider correlation, mouse output, interactive visual keyboard/mouse mapping, common Easy Mode presets, output selection in First Run Setup, and profile-aware collapsible output monitors.

Follow-up usability and release-engineering changes add:

- Fail-closed Authenticode signing for first-party binaries, setup, and uninstaller; SHA-256 release manifests and independent verification; and a disposable-machine retained-upgrade/rollback/repair/uninstall acceptance runner. Certificate-backed release output and publication have been completed; no signing credentials are stored in source control. Clean-machine acceptance remains required.
- A stable .NET 10.0.302 SDK pin with preview roll-forward disabled, resolving the preview-toolchain blocker.
- Correct Project Health classification so completed and intentionally deferred history is no longer reported as active technical debt.

Current automated validation:

- 434 tests passed with 58.68 percent merged runtime line coverage (19,472 of 33,183 lines).
- Debug and Release builds completed with zero warnings and zero errors on stable .NET SDK 10.0.302.
- Architecture validation reported zero errors, warnings, and suggestions.
- Isolated fresh-install, existing Easy/Advanced, profile package, Macro Editor, Node Editor, Script Workbench, Diagnostics, coordinated shutdown, and Safe Mode WPF smoke passed.

- Optional branching graphs in the Beta Node Editor, including typed direct ports, AND/OR/NOT/Compare nodes, one registered internal pass-through extension node, graph validation, live per-node timing, workspace-persisted positions/zoom, and large-graph viewport culling.
- Graph-safe mapping duplication and profile comparison/merge without changing graph-free mappings.
- Trusted scripting hardening with signed .hotasscript packages, local publisher trust, explicit host permissions, bounded resources, and a fail-closed policy that keeps untrusted packages disabled.
- An Experimental Script Workbench for local Lua creation, syntax validation, atomic save, runtime reload, and live trust/quota/error diagnostics.

- Confirmed profile deletion with automatic selection of a remaining profile.
- Restored main-window size, position, and maximized state across restarts.
- Stable Devices-grid columns whose user widths and display order survive resizing and restart.

Profile schema v7 added mapping-owned hat direction/center/diagonal and pointer configuration. Profile schema v8 promotes enabled mapping-behavior descriptors to runtime authority while retaining old behavior fields as migration and authoring projections. Profile schema v9 adds optional versioned branching graphs; existing mappings remain graph-free after migration. Settings schema v5 stores interface mode, visual keyboard layout, and the Advanced provider-duplicate override. Workspace schema v3 stores shell geometry, Devices-grid layout, and bounded per-mapping graph positions/zoom. Existing Xbox and keyboard mappings migrate after an exact backup; pre-mode users retain Advanced navigation.

The virtual Xbox/ViGEm implementation, profile store, Mapping Engine, Transform Engine, scheduler, Runtime Signal Cache, and existing profile format lineage were extended rather than replaced. Physical WinWing/PlayStation/SendInput validation remains explicitly pending in the compatibility matrix.

See `NEXT_IMPLEMENTATION_COMPLETION_REPORT.md` for final build/test evidence and `HAT-COMPATIBILITY.md` for automated versus physical results.

## 0.25.0-dev - Long-Term Governance Foundation

HOTASBridge now records its long-term platform vision, stable architectural foundations, project governance, and formal milestone-review policy in the repository. A machine-readable schedule and validator protect Version 1.0 acceptance, external plugin loading, branching Node Editor work, Scripting promotion, and profile schema changes beyond v6.

The Chapter 25 baseline review accepts the current runtime foundations with tracked follow-ups. It does not change input, mapping, output, profile, plugin, script, or UI behavior and does not declare Version 1.0 ready.

Developer validation now produces an architecture-review report alongside architecture, build, test, coverage, documentation, schema, and artifact evidence.

### Validation Snapshot

- 208 automated tests passed with 55.98 percent merged runtime line coverage.
- Debug and Release builds completed with zero warnings and zero errors.
- Architecture validation reported 0 errors, 2 tracked warnings, and 10 tracked suggestions.
- Architecture review validation passed with 2 satisfied and 4 planned gates.
- Fresh-install, existing-install, and Safe Mode WPF startup smoke passed.
- Release readiness remains blocked by five known blockers and eight pending manual checks.
## 0.24.0-dev - Offline AI Advisor Foundation

Status: internal AI-workflow foundation. Automated release validation passes, while public release remains blocked on the recorded hardware, signing, clean-machine, migration, runtime-policy, and soak checks.

### Added

- Immutable, bounded AI evidence and explanation contracts in Core.
- Deterministic offline advisor with no mutation, network, hardware, output, or persistence capability.
- AI Explain actions in Device Inspector, Mapping Editor, Signal Flow Inspector, Profile Health, and Output Monitor.
- Copyable timestamped explanation reports and explicit approval-required suggestions.
- Privacy, integration, feature-policy, and deferred remote-provider guidance.

### Validation Snapshot

- 208 automated tests passed, 0 failed, and 0 skipped.
- Debug and Release builds succeeded with zero warnings and zero errors.
- Architecture validation reported 0 errors, 2 tracked warnings, and 10 tracked suggestions.
- Merged runtime coverage measured 55.98 percent (11,809 of 21,094 source lines).
- Isolated fresh-install, existing-install, and Safe Mode WPF startup smoke passed.

### Compatibility

- Profile schema remains v6; settings schema remains v3.
- Plugin API and Script API remain 1.0.
- Existing input, mappings, transforms, profiles, macros, scripts, and output behavior are unchanged.
- Remote AI providers, autonomous changes, learning, voice, and generative profile workflows are not enabled.

## 0.23.0-dev - Engineering Governance

Status: internal engineering-governance milestone. Automated release validation passes, while public release remains blocked on the recorded hardware, signing, clean-machine, migration, runtime-policy, and soak checks.

### Added

- Versioned architecture policy and BCL-only validator with text/JSON output.
- Automated project direction, cycle, WPF isolation, and solution-membership gates.
- Incremental public API documentation, implementation leakage, service construction, and test-reference checks.
- Contribution, code-style, review, ADR, and architecture-validation guidance.

### Validation Snapshot

- 197 automated tests passed, 0 failed, and 0 skipped.
- Debug and Release builds succeeded with zero warnings and zero errors.
- Architecture validation reported 0 errors, 2 tracked warnings, and 10 tracked suggestions.
- Merged runtime coverage remained 56.61 percent (11,454 of 20,234 source lines).
- Isolated fresh-install, existing-install, and Safe Mode WPF startup smoke passed.

### Compatibility

- Profile schema remains v6; settings schema remains v3.
- Plugin API and Script API remain 1.0.
- No runtime subsystem, mapping behavior, or profile format was rewritten for Chapter 23.

## 0.22.0-dev - Deployment and First Run Foundation

Status: internal deployment milestone. The installer source compiles, but public release remains blocked on production signing and clean-machine acceptance.

### Added

- Inno Setup package source for per-user/machine-wide install, upgrade/repair, pre-upgrade backup, and category-specific uninstall preservation.
- Skippable First Run Setup covering prerequisites, devices, driver, profile, live input, starter mapping, and Xbox output.
- Shared ViGEmBus status/install service with explicit user action and normal Windows UAC.
- Versioned deployment backup/restore and offline Stable/Beta update architecture.

### Validation Snapshot

- 188 automated tests passed, 0 failed, and 0 skipped before final release-validation measurement.
- Debug build succeeded with zero warnings and zero errors.
- PowerShell deployment backup/restore passed an isolated round-trip.
- Inno Setup 6.6.1 compiled the setup package successfully.

### Compatibility

- Profile schema remains v6; existing mappings and profiles are unchanged.
- Settings advance additively from v2 to v3.
- Existing installations skip automatic First Run Setup and can reopen it from About.
- ViGEmBus remains optional for startup and required only for virtual Xbox output.
- Driver install/removal is never silent.

### Remaining Release Gates

- Production Authenticode signature and checksums.
- Clean Windows 10/11 install, upgrade, repair, rollback, and uninstall validation.
- Retained-build migration rehearsal and formal hardware/output acceptance.

## 0.21.0-dev - Profile Library Foundation

Status: internal profile-management milestone. This build is not the supported Version 1.0 release; online community services remain disabled because they are not implemented.

### Added

- Searchable local Profile Library and expanded optional profile metadata.
- Compatibility preview before import, with safe cancel and backed-up active-profile replacement.
- Versioned profile/template packages and selected mapping/device-group export.
- Side-by-side Profile Comparison Tool with selected merge and JSON/HTML reports.

### Validation Snapshot

- 180 automated tests passed, 0 failed, and 0 skipped.
- Debug WPF compilation succeeded with zero warnings and zero errors.
- Merged runtime line coverage is 56.30 percent (11,060 of 19,644 lines).
- Final Release evidence is recorded by the Chapter 21 validation artifact and `PROJECT_HEALTH.json`.

### Compatibility

- Existing raw JSON profiles still import and export.
- Profile schema advances additively from v5 to v6; settings remain v2, plugin API remains 1.0, and Script API remains 1.0.
- Missing devices and unknown transforms are reported and retained.
- Vendor converters, cloud sync, ratings, comments, and online package acquisition remain deferred.

## 0.20.0-dev - Lua Scripting Foundation

Status: internal Experimental-feature milestone. This build is not the supported Version 1.0 release, and scripting is disabled by default.

### Added

- Independently versioned Script API 1.0 with no internal HOTASBridge implementation references.
- Optional Lua discovery, lifecycle, RuntimeSignal/system events, variables, OutputAction requests, profile/mapping requests, notifications, logging, timers, and diagnostics.
- Central-scheduler execution, instruction budgets, transactional side effects, per-script failure isolation, and shutdown timer cleanup.
- Constrained in-process host surface without file, OS, debug, dynamic-load, network, native, or automatic .NET interop access.

### Validation Snapshot

- 172 automated tests passed, 0 failed, and 0 skipped.
- Debug and Release builds succeeded with zero warnings and zero errors.
- Merged runtime line coverage is 55.78 percent (10,253 of 18,382 lines).
- Eight dedicated tests protect the Script API boundary, sandbox surface, instruction budget, timer ownership, discovery, and failure isolation.

### Compatibility

- Existing mappings, macros, profiles, and outputs execute unchanged while scripting is disabled.
- Profile schema remains v5; settings remain v2, plugin API remains 1.0, and Script API is independently versioned at 1.0.
- Scripts are external files under the application Scripts directory and are never embedded in profiles.
- The in-process sandbox is not a trust boundary for hostile code; signed packages, permissions, memory quotas, and stronger isolation remain deferred.
## 0.19.0-dev - Macro Automation Foundation

Status: internal Beta-feature milestone. This build is not the supported Version 1.0 release.

### Added

- Optional Macro Engine with RuntimeSignal/system triggers, conditions, ordered actions, repeats, and runtime variables.
- Central-scheduler timers, delays, waits, cancellation, and held-output cleanup.
- Beta Macro Debugger with Pause, Resume, Stop, Restart, Step One Action, variables, scheduler state, timing, and errors.
- Profile schema v5 and additive migration for macro and variable definitions.
- Shared macro telemetry and Signal Flow stage diagnostics.

### Validation Snapshot

- 164 automated tests passed, 0 failed, and 0 skipped.
- Debug and Release builds succeeded with zero warnings and zero errors.
- Merged runtime line coverage is 54.81 percent (9,537 of 17,401 lines).
- Macro output release, inactive-runtime protection, repeat safety, migration, and persistence have dedicated regression tests.

### Compatibility

- Existing mappings and profiles without macros execute unchanged.
- Profile schema advances additively to v5; settings remain v2 and plugin API remains 1.0.
- Runtime variable values and macro execution state are never persisted.
- Disabling `macro-engine` removes runtime composition/navigation without deleting profile macro configuration.

## 0.18.0-dev - Visual Node Editor Foundation

Status: internal Beta-feature milestone. This build is not the supported Version 1.0 release.

### Added

- Beta Visual Node Editor over every existing profile mapping.
- Typed input, transform, output, and connection projection with graph validation.
- Search, templates, settings, ordering, multi-select, clipboard, undo/redo, zoom, pan, drag, minimap, and live diagnostics.
- Immediate synchronization with Mapping Editor, Mapping Explorer, Transform Editor, profile health, and the live runtime mapping.
- Dedicated Node Editor, graph model, and Chapter 18 documentation.

### Validation Snapshot

- 153 automated tests passed, 0 failed, and 0 skipped.
- Debug WPF compilation succeeded with zero warnings and zero errors.
- Merged runtime line coverage is 57.13 percent (8,546 of 14,959 lines).
- Isolated Safe Mode UI Automation selected Node Editor and confirmed the graph workspace rendered.

### Compatibility

- Profile schema remains v4; graph projections and positions are not serialized.
- Application settings remain v2 and plugin API remains 1.0.
- Disabling the `node-editor` Beta feature removes its page without changing mappings.
- Branching, logic/plugin/custom nodes, direct connection drawing, and persisted graph positions remain deferred.

## 0.17.0-dev - Release Preparation Foundation

Status: internal development milestone. This build is not the supported Version 1.0 release.

### Added

- Project Health page with architecture, documentation, measured test coverage, hardware evidence, known issues, technical debt, manual acceptance, and active feature flags.
- Versioned machine-readable `PROJECT_HEALTH.json` with validated fail-safe loading.
- Repeatable release-validation script covering restore, Debug build, automated tests, merged coverage, Release build, documentation, schemas, and expected artifacts.
- User Guide, Developer Guide, final-validation guide, and Chapter 17 acceptance evidence.
- Coverage collection for both Core and Integration test projects.

### Validation Snapshot

- Baseline before Chapter 17: 137 tests passed.
- Chapter 17 final validation: 142 tests passed, 0 failed, and 0 skipped.
- Measured runtime line coverage: 58.59 percent (8,177 of 13,957 lines) across Core, Input, Output, and Infrastructure.
- WPF behavior remains covered by UI Automation and visual validation rather than the runtime line percentage.

### Compatibility

- Profile schema remains version 4.
- Application settings schema remains version 2.
- Plugin manifest schema remains version 1 and API remains 1.0.
- Existing Xbox, keyboard, HOTAS input, mapping, workspace, and recovery behavior is preserved.

### Known Release Blockers

- Formal WinWing Orion 2 Stick and Throttle release checklist.
- Signed installer, checksum, upgrade, uninstall, and clean-machine validation.
- Historical-build migration rehearsal.
- Supported Windows and production .NET runtime matrix sign-off.

### Outstanding Evidence

- Formal ViGEm Xbox output acceptance record.
- Application-level keyboard output acceptance.
- Long-duration runtime soak and profiler baseline.
- Public beta feedback and exact published-artifact verification.

### Release Decision

Automated validation may pass while release readiness remains blocked. Project Health is the source of truth for unresolved manual and external gates. Version 1.0 must not be declared complete until every required acceptance item is recorded as complete.

# Changelog

## Unreleased

### Synthetic Keyboard Holds

- Repeated held keyboard outputs through the centralized scheduler using the active Windows keyboard delay and repeat-rate settings.
- Kept only the main key repeating for modifier chords while the modifiers remain continuously held.
- Retained standalone Down/Press actions after successful macro completion and released them on matching Up, toggle-off, profile reconfiguration, runtime stop, failure, or output reset.
- Suppressed the complete physical down/repeat/up sequence when an active keyboard or mouse macro presses or toggles that same host control, preventing duplicate first input.
- Added Core and output integration regressions for completion persistence, repeat dispatch, timer cancellation, and runtime-stop cleanup.
- Verified 422 passing tests, clean Debug and Release builds, the full isolated WPF smoke suite, and zero architecture findings.

### Macro Toggle Ownership

- Confirmed keyboard toggles latch the selected key down on first execution and release it on the next.
- Suppressed the original physical keyboard or mouse event when an active same-control press or toggle macro owns its host trigger.
- Suppressed repeated physical key-down events and kept down/up suppression paired for each press.
- Added focused Core and keyboard-output regression coverage.
- Verified 420 passing tests, clean Debug and Release builds, the full isolated WPF smoke suite, and zero architecture findings.

### Macro Editor Bulk Clear

- Added a one-click Clear recording command for the completed live recording preview.
- Added Clear all to both Easy and Advanced action editors; it removes timeline and grouped actions while preserving the macro and sequence/ping-pong groups.
- Kept recording-preview clearing separate from profile action mutation and disabled it while capture is active.
- Added Core regression coverage and WPF smoke assertions for both controls.
- Verified 418 passing tests, clean Debug and Release builds, the fresh/existing Easy/Advanced/Safe Mode WPF smoke suite, and zero architecture findings.

### Profile Library Responsiveness

- Stopped automatic profile activation from rebuilding the running-application picker every five seconds; the picker now changes only after an explicit refresh.
- Replaced per-item process-picker collection churn with one immutable snapshot and enabled recycling virtualization for its dropdown.
- Moved Windows HID and Raw Input discovery work off the WPF thread so startup and device refresh remain responsive while native enumeration runs.
- Removed Difficulty and Aircraft from the active Profiles UI while preserving both fields for existing profile compatibility.
- Added a direct Save current as template action backed by the existing versioned template package exporter.
- Verified 417 passing tests, clean Debug and Release builds, the fresh/existing Easy/Advanced/Safe Mode WPF smoke suite, and zero architecture findings.

### Backlog Milestone - Release Engineering and Acceptance Tooling

- Added certificate-store SignTool integration for first-party application binaries and conditional Inno signing of setup and uninstaller with RFC 3161 timestamps.
- Added explicit unsigned-development and fail-closed signed-release modes; production mode rejects absent, invalid, expired, private-key-less, or unsuitable certificates.
- Added versioned release manifests, standard SHA-256 sums, an independent verifier, and positive/tamper/traversal/checksum/unsigned-release self-tests.
- Added a disposable-machine-only acceptance runner for retained-build upgrade, deployment-backup rollback, repair, signed installed payloads, preserve-by-default uninstall, and all 16 user-data choice combinations.
- Completed certificate-backed signing and publication without storing signing credentials in source control; clean Windows acceptance evidence remains an explicit external release blocker.

### Backlog Milestone - Signed Local Profile Packages

- Added signed `.hotasbundle` profile archives with ECDSA P-256/SHA-256 provenance, Windows current-user CNG publisher identity, offline trust allow-list, and blocking invalid-signature compatibility issues.
- Added bounded PNG/JPEG package screenshots and cryptographically gated import-preview thumbnails without changing active profile schema or runtime state.
- Added conservative Thrustmaster T.A.R.G.E.T. `.tmc` import for documented simple single-key `MapKey` statements; converted mappings are disabled and unsupported expressions receive line-numbered warnings.
- Preserved legacy JSON profile/template import/export as an explicit format and kept online/community/cloud services excluded.
- Verified 401 passing tests, 58.27% merged runtime coverage (`18,636/31,982`), clean Debug and Release builds, and zero architecture findings.

### Backlog Milestone - Legacy Composite Input Retirement

- Removed the unreferenced `CompositeInputCatalog` and `CompositeInputEventSource` aggregate compatibility classes.
- Preserved the active per-provider `IInputDeviceCatalog` and `IInputEventSource` implementations behind `InputProviderAdapter`.
- Preserved `CompositeInputProvider` as the sole app-facing aggregation, deduplication, lifecycle, health, and RuntimeSignal boundary.
- Verified there were no source, test, composition, or reflection-name callers before removal.
- Verified 360 passing tests, 58.62% merged runtime coverage (`15,992/27,279`), clean Debug and Release builds, and zero architecture findings.

### Backlog Milestone - Windows Container IDs

- Extended existing HID interface enumeration to retain `SP_DEVINFO_DATA` and read `DEVPKEY_Device_ContainerId` through `SetupDiGetDevicePropertyW`.
- Validated Container ID properties as non-empty GUID values and retained null fallback behavior when Windows does not expose the property.
- Populated `DeviceIdentity.ContainerId` without changing the Stable ID algorithm, so existing mappings and profiles remain compatible.
- Updated profile reconciliation to backfill newly available identity metadata even when the Stable ID is unchanged, while retaining a known Container ID across a transient same-device lookup failure.
- Added native parser/enumeration, same-ID reconciliation, and JSON round-trip regressions.
- Verified 360 passing tests, 58.52% merged runtime coverage (`15,992/27,329`), clean Debug and Release builds, and zero architecture findings.

### Backlog Milestone - Native Windows Device Notifications

- Added an Input-layer `IInputDeviceNotificationSource` boundary and Windows implementation backed by a private Win32 message-only window plus HID `RegisterDeviceNotification`.
- Native arrival, removal, and topology events now wake the existing serialized `CompositeInputProvider` discovery path after a 150 ms burst-coalescing delay.
- Native mode retains a 30-second safety poll; startup failure or unavailability automatically restores the prior two-second polling cadence without failing application startup.
- Added notification availability, received-event, notification-refresh, and fallback-refresh telemetry while preserving existing lifecycle events and UI refresh behavior.
- Added integration coverage for immediate notification refresh, startup-failure fallback, and native registration/start/stop without physical hardware.
- Verified 358 passing tests, 58.44% merged runtime coverage (`15,925/27,248`), clean Debug and Release builds, fresh/existing Easy/Advanced/Safe Mode WPF smoke, and zero architecture findings.

### Backlog Milestone - Configurable Analog Filters

- Centralized filter modes, defaults, supported ranges, parsing, and mapping validation in `FilterConfiguration`.
- Preserved the active descriptor-based Moving Average, Exponential Smoothing, and Median Filter algorithms plus per-mapping/transform runtime state and diagnostics.
- Replaced raw filter key/value authoring with a focused Transform Editor algorithm selector and mode-specific alpha/window controls.
- Added critical profile-health reporting for invalid enabled filter configuration while retaining safe runtime defaults for legacy data.
- Added deterministic regressions for Exponential Smoothing, Median Filter, defaults, invalid settings, and profile validation.
- Verified 355 passing tests, 58.26% merged runtime coverage (`15,662/26,882`), clean Debug and Release builds, fresh/existing Easy/Advanced/Safe Mode WPF smoke, and zero architecture findings.

### Backlog Milestone - Typed Runtime Event Bus

- Added `IRuntimeEventBus` and one process-wide `RuntimeEventBus` with exact-type routing, deterministic subscription order, idempotent subscription leases, isolated/logged handler failures, and total/per-type health counters.
- Added immutable messages for RuntimeSignal publication, runtime-stage diagnostics, profile save/skip results, plugin lifecycle transitions, and output-plugin diagnostic snapshots.
- Mirrored current publishers onto the bus without removing compatibility events or changing the authoritative cache, telemetry, profile, plugin, or output state boundaries.
- Reused the composition root's shared Plugin Catalog in Output Manager so lifecycle events and diagnostics now observe the same state.
- Added focused Core and integration regressions for routing, order, failure isolation, unsubscription, counters, and all five publisher families.
- Verified 351 passing tests, 58.25% merged runtime coverage (`15,566/26,724`), clean Debug and Release builds, fresh/existing Easy/Advanced/Safe Mode WPF smoke, and zero architecture findings.

### Backlog Milestone - Discrete Control Visuals

- Replaced the basic hat value tile with a live 3x3 cardinal/diagonal/center grid while retaining raw value, direction count, centered state, center press, provider, last change, mapping count, and Learn Mode highlighting.
- Added compact encoder cards with last-direction emphasis plus independent CW, CCW, total pulse, and last-pulse diagnostics.
- Added switch cards with a normalized position track, percentage, current/previous value, last change, and Learn Mode highlighting.
- Removed the obsolete generic discrete-control row template; all visuals continue to consume cached runtime signals through the existing Device Inspector view models.
- Verified clean Debug and Release compilation, zero architecture findings, and fresh/existing Easy/Advanced/Safe Mode startup smoke; runtime test and coverage totals remain 342 passing and 58.03% (`15,311/26,383`).

### Backlog Milestone - ViGEm Backend Error Counters

- Added an immutable virtual-gamepad backend diagnostic snapshot with cumulative connection, report-submission, cleanup, and total failure counters plus last-failure evidence.
- Isolated the native ViGEm client/controller lifecycle behind an internal session boundary so failure paths can be tested without a driver or virtual controller.
- Published backend counters and health through Xbox plugin diagnostics, shared runtime telemetry, Signal Flow stage warnings, and Output Monitor.
- Preserved safe shutdown by containing disconnect/dispose failures while retaining their diagnostic evidence.
- Made the existing batched-logging coverage deterministic by synchronizing its regression test with the actual writer batch window; production timing is unchanged.
- Verified 342 passing tests, 58.03% merged runtime coverage (`15,311/26,383`), clean Debug and Release builds, WPF startup smoke, and zero architecture findings.

### Backlog Milestone - Batched Logging and Retention

- Extended the existing bounded asynchronous `JsonFileLog` with 100 ms/128-event write batches while preserving explicit flush and coordinated shutdown semantics.
- Added a configurable 1-365 day retention policy, defaulting to 14 days, that deletes only expired `hotasbridge-YYYYMMDD.jsonl` files.
- Added queue, rejected-event, event-write, batch-write, retention-day, removed-file, and retention-failure telemetry through the shared runtime telemetry service.
- Advanced application settings additively to schema v6 and exposed log retention under Settings > Diagnostics.
- Added regression coverage for batching, flush completeness, owned-file retention safety, defaults, migration, and range clamping.
- Verified 338 passing tests, 57.66% merged runtime coverage (`15,161/26,296`), WPF startup smoke, clean Debug and Release builds, and zero architecture findings.

### Backlog Milestone - Main Window Decomposition Complete

- Removed the unreachable `XboxOutputLegacy` and `ProfilesLegacy` compatibility blocks from `MainWindow.xaml`.
- Verified Easy and Advanced navigation use the canonical `XboxOutput` and `Profiles` keys backed by `OutputMonitorView` and `ProfilesView`.
- Closed TD-010: every primary page is now a focused WPF view and the shell contains only navigation, workspace composition, global commands, and the status bar.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Settings View Extraction

- Extracted the inline seven-category Settings page into `Views/SettingsView.xaml` while preserving application, workspace, device, profile, output, diagnostics, deployment, and update bindings.
- Bound Developer feature build text, summary, pending-restart state, and rows directly to `FeatureFlagSettingsViewModel`.
- Removed the feature collection/text shell aliases plus the feature-settings property subscription and forwarding handler.
- Preserved the existing WPF smoke that opens Settings and switches Interface Mode from Easy to Advanced through the extracted view.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Axis Curves View Extraction

- Extracted the inline Axis Curves page into `Views/AxisCurvesView.xaml` without changing its three-column layout or `CurveEditorControl` preview inputs.
- Bound targets, selection, settings, curve type, live raw/processed values, title, Save, and Reset directly to `AxisCurveEditorViewModel`.
- Moved the curve-type catalog into the editor owner and removed the shell curve aliases, subscription, and property-forwarding bridge.
- Extended Advanced Mode WPF smoke to open Axis Curves and assert its curve-type ComboBox.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, expanded WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Editor View Extraction

- Extracted the complete inline Mapping Editor into `Views/MappingEditorView.xaml` while preserving its finite three-column measurement and 450x220 Xbox/mouse visualizers.
- Bound device tabs, live controls, saved rows, Easy presets, and Learn Mode presentation/commands directly to their focused owners.
- Removed the matching Mapping collections, Easy-preset, and Learn Mode shell aliases plus the Learn property-forwarding bridge.
- Preserved the existing interactive Easy Mode smoke that opens Mappings and verifies the selected `Hat to Xbox D-pad` preset, guarding against the prior page lock-up.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Device Inspector View Extraction

- Extracted the complete inline Device Inspector visual tree into `Views/DeviceInspectorView.xaml` without changing live-control, diagnostics, mapping-preview, export, freeze, or AI Explain behavior.
- Bound per-device tabs directly to `ProfileDeviceTabs.Tabs` while retaining the shared selected-device property used by Mapping Editor, Learn Mode, curves, macros, and diagnostics.
- Added an empty-state-safe `Device inspector tabs` automation identity and Easy Mode Test Inputs smoke coverage.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, expanded WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Dashboard View Extraction

- Extracted the inline Dashboard from `MainWindow.xaml` into `Views/DashboardView.xaml`.
- Preserved the fixed 450x220 recent-input and generated-Xbox panels, profile-health summary, runtime counters, Easy Mode notice, and all quick actions.
- Bound recent activity directly to `RuntimeActivity.RecentEvents` and removed the now-unused `MainViewModel.RecentEvents` proxy.
- Extended isolated WPF smoke to open Home in Easy Mode and assert the recent-input and generated-Xbox panels.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, expanded WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Devices View Extraction

- Extracted the inline Devices page from `MainWindow.xaml` into `Views/DevicesView.xaml`.
- Bound search, filters, input-source mode, demo visibility, visible count, and rows directly to `DeviceBrowserViewModel`, removing the corresponding shell aliases and property-change bridge.
- Kept profile membership commands in the shell because they coordinate profile mutation, inspector rebuilding, input monitoring, and runtime refresh across subsystem owners.
- Moved Devices-grid width/order capture and restore into the focused view so existing workspace persistence remains intact.
- Extended isolated WPF smoke to open Input Devices in Easy Mode and assert the extracted grid is present.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, expanded WPF smoke, and an architecture report with no findings.

### Backlog Milestone - Diagnostics View Extraction

- Extracted the inline Advanced Diagnostics page from `MainWindow.xaml` into `Views/DiagnosticsView.xaml`.
- Bound both the page and workspace Diagnostics pane directly to `RuntimeActivity.Diagnostics` and removed the now-unused `MainViewModel.Diagnostics` collection proxy.
- Extended isolated WPF smoke from Easy Mode through Advanced Mode, selected Diagnostics, and asserted the extracted runtime diagnostics list is present.
- Preserved bounded activity retention, event formatting, runtime publication, navigation entry effects, and all existing diagnostic consumers.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, expanded WPF smoke, and an architecture report with no findings.
### Backlog Milestone - About View Extraction

- Extracted the self-contained About page from `MainWindow.xaml` into `Views/AboutView.xaml` without moving command or application state ownership.
- Preserved Easy and Advanced visibility, version/driver status, offline ViGEmBus and ViGEm.Client license access, and First Run Setup recovery.
- Extended the isolated WPF smoke workflow to navigate to About in Easy Mode and assert the bundled-license link plus setup-wizard action.
- Closed the TD-006 telemetry foundation after confirming the shared versioned session model, atomic history store, analysis, and Debug profiler workflow satisfy its original boundary; optional retention and extra formats remain normal future extensions.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, fresh/existing/Safe Mode WPF smoke, and an architecture report with no findings.
### Backlog Milestone - HID Value Parsing Coverage

- Extracted the duplicated Windows `HIDP_VALUE_CAPS` layout and usage enumeration into one internal Input-layer parser used by device discovery and live report processing.
- Moved signed bit-width conversion, logical-range clamping, hat null-value preservation, and axis normalization into the testable parser.
- Changed asymmetric bipolar normalization to scale each side by its own declared endpoint so common `-32768..32767` and unusual asymmetric ranges reach exact `-1` and `+1` values.
- Added six Windows integration regressions for native struct sizes, single/ranged/malformed usage caps, 8/12/16/32-bit sign extension, unsigned clamping, hat null values, and axis endpoints.
- Verified 337 passing tests, 57.55% merged runtime coverage (`15,059/26,167`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Reusable Telemetry Session History

- Added Core `RuntimeTelemetrySession`, sample, summary, comparison, analysis, and `IRuntimeTelemetrySessionStore` contracts so recording history no longer belongs to WPF.
- Added `JsonRuntimeTelemetrySessionStore` with version validation, atomic writes, opaque storage IDs, path-traversal rejection, and corrupt/unreadable-file isolation under `Diagnostics/PerformanceSessions`.
- Adapted the Debug-only Performance Profiler to consume the shared analysis and storage services without changing its recording, load, save, or comparison workflow.
- Added four focused Core and integration regressions and verified 331 passing tests, 57.36% merged runtime coverage (`15,007/26,165`), clean Debug and Release builds, fresh/existing/Safe Mode WPF smoke, and an architecture report with no findings.

### Backlog Milestone - MainViewModel Decomposition Foundation Complete

- Closed TD-002 at the architecture-foundation level after auditing 27 focused page/presentation view models plus Core profile, device, input-monitoring, runtime-mapping, and runtime-session coordinators.
- Confirmed that the remaining `MainViewModel` work is application lifecycle, Dispatcher sampling, composition-facing compatibility proxies, and explicit cross-page reactions rather than ownership of extracted subsystem behavior.
- Kept XAML page modularization and eventual compatibility-proxy removal under TD-010; no callback-only coordinator was introduced around legitimate shell orchestration.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Feature Flag Settings Ownership

- Added `FeatureFlagSettingsViewModel` as the owner of visible feature rows, Debug/Release text, enabled summary, startup-state comparison, pending-restart state, and row override handling.
- Preserved application-settings override persistence, Project Health refresh, initialized-session restart notice, startup-fixed output/AI availability, and all existing MainWindow bindings through shell callbacks and proxies.
- Removed the mutable pending-change field plus feature row rebuild and toggle implementations from `MainViewModel`.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, two consecutive isolated WPF smoke runs, and an architecture report with no findings.

### Backlog Milestone - Runtime Activity Feed Ownership

- Added `RuntimeActivityViewModel` as the owner of Dashboard Recent Events and the input Diagnostics presentation feed.
- Preserved meaningful-change filtering, the existing global 100 ms analog activity throttle, normalized-value formatting, 150-entry Recent Events retention, 200-entry connection-diagnostic retention, and startup/Safe Mode ordering.
- Removed two mutable shell collections, the recent-event timestamp field, and feed formatting/retention loops from `MainViewModel`; event dispatch, recovery, status, telemetry, and runtime work remain shell responsibilities.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, two consecutive isolated WPF smoke runs, and an architecture report with no findings.

### Backlog Milestone - Device Browser Synchronization Ownership

- Extended `DeviceBrowserViewModel` to replace discovered device rows and synchronize each row's enabled active-profile membership.
- Removed Devices collection clear/create/populate logic plus three membership-resynchronization loops from `MainViewModel`.
- Preserved `IDeviceCoordinator` discovery and profile mutation, selected-row intent, profile view rebuilds, duplicate warnings, input monitoring, telemetry, logging, and status behavior.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, two consecutive isolated WPF smoke runs, and an architecture report with no findings.

### Backlog Milestone - Profile Collections Ownership

- Added `ProfileCollectionsViewModel` as the owner of the observable profile and recent-profile collections.
- Routed startup loading, New, template creation, Duplicate, Save As, Rename refresh, Delete, local package import/replace, and First Run profile additions through the focused owner.
- Centralized recent-profile deduplication, newest-first ordering, metadata snapshots, ten-item retention, deletion, and presentation synchronization while leaving settings persistence in the shell.
- Preserved `ListCollectionView`, active-profile transitions, profile persistence/validation, runtime refresh, and existing XAML bindings through collection proxies.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, two consecutive isolated WPF smoke runs, and an architecture report with no findings.

### Backlog Milestone - Profile Device Tabs Ownership

- Added `ProfileDeviceTabsViewModel` as the owner of the Device Inspector tab collection and its deterministic rebuild from enabled active-profile devices matched to the current detected-device list.
- Preserved inspector construction, selected-device restoration, mapping/curve/macro/signal-flow refresh, telemetry, scripting context, property notifications, and input-monitoring restart as shell effects.
- Replaced the mutable `MainViewModel.DeviceTabs` collection with a compatibility proxy and removed its clear/match/create loop from the shell.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, two consecutive isolated WPF smoke runs, and an architecture report with no findings.

### Backlog Milestone - Mapping Authoring Workflow Ownership

- Added `MappingAuthoringViewModel` as the owner of Mapping Editor create/update orchestration, Easy presets, demo and learned mapping creation, edit loading/cancellation, output validation, and selected-mapping deletion.
- Preserved all existing XAML command and state bindings through shell proxies while keeping mapping mutation in the tested Core `MappingProfileEditor`.
- Removed editor-specific mapping mutation and edit-state helpers from `MainViewModel`; cross-page refresh, navigation, runtime transitions, selection clearing, and status publication remain shell effects.
- Hardened WPF UI Automation navigation and ComboBox helpers to reacquire the current window tree from its native handle on each retry; fresh, existing-install Easy Mode, and Safe Mode smoke passed three consecutive runs.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Derived Mapping Collections Ownership

- Added `MappingCollectionsViewModel` as the owner of saved-mapping rows, selected-device control options, duplicate-device warnings, and their deterministic rebuild rules.
- Preserved selected-control clearing, runtime mapping transitions, page refreshes, and all existing XAML bindings through shell coordination and collection proxies.
- Added per-step coordinated-shutdown events and hardened WPF smoke to wait for post-wizard navigation readiness using its configured timeout.
- A/B smoke against the previous validated commit reproduced the old race; the hardened fresh/existing/Safe Mode smoke then passed twice consecutively.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Notification Center Ownership

- Added `NotificationCenterViewModel` as the owner of the current shell status and newest-first 100-entry activity history.
- Preserved quiet startup, duplicate-status suppression, existing information/warning/error classification, and all MainWindow/workspace bindings through compatibility proxies.
- Removed mutable status/history storage and notification-classification logic from `MainViewModel` while retaining cross-subsystem status publication callbacks.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, isolated WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Command Presentation Ownership

- Added `MappingCommandsViewModel` as the owner of demo-mapping, Easy preset, create/update, cancel-edit, and delete-selection command instances plus their enablement refresh.
- Moved Axis Curves Save and Reset command instances into `AxisCurveEditorViewModel`.
- Preserved every existing MainWindow command binding through compatibility proxies; mapping mutation remains in the tested Core `MappingProfileEditor` and shell orchestration remains unchanged.
- Verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, isolated WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Profile Workflow Ownership

- Added a UI-independent `IProfileManagementCoordinator` for forced save, normalized creation, templates, duplicate/Save As, rename, export, delete, and transactional last-profile replacement rollback.
- Added `ProfileCommandsViewModel` for the nine profile command instances plus operation-name/template selection state, preserving every existing shell binding through proxies.
- Added `IProfileDialogService` so profile/package import and export file pickers plus destructive delete confirmation no longer live in `MainViewModel` partials.
- Added five focused Core regressions and verified 327 passing tests, 57.27% merged runtime coverage (`14,850/25,929`), clean Debug and Release builds, isolated WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Navigation Ownership

- Added `NavigationViewModel` as the owner of visible destinations, selected item, null-safe page key, Easy/Advanced rebuilds, and the shared navigation command.
- Centralized the Advanced destination catalog and feature requirements beside the existing Easy Mode policy in Core, with direct tests for gated pages and Easy Mode isolation.
- Removed the mutable navigation field, catalog builder, and navigation command implementation from `MainViewModel`; page-entry refresh/status effects remain behind one narrow callback and existing XAML bindings remain compatible.
- Verified 322 passing tests, 57.29% merged runtime coverage (`14,785/25,807`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Physical Keyboard Capture Command Ownership

- Added `IKeyboardCaptureDialogService` so WPF keyboard-capture window creation, ownership, and session composition no longer live in `MainViewModel`.
- Moved primary/negative capture and clear command instances, duplicate-warning callbacks, assignment changes, output-mode selection, and status messages into `MappingEditorSelectionViewModel`.
- Preserved every existing MainWindow command binding through compatibility proxies and retained the physical hook's generated-input filtering and duplicate-assignment behavior.
- Verified 320 passing tests, 57.31% merged runtime coverage (`14,752/25,739`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Authoring Boundary

- Added UI-independent `MappingAuthoringRequest` and `MappingProfileEditor` transactions for add, update, remove, output configuration, output enablement, and compatibility synchronization.
- Moved Xbox, keyboard, mouse, hat, pointer, axis, and bipolar PWM mutation rules out of `MainViewModel` while preserving WPF validation, selection, status, and refresh behavior.
- Invalid PWM updates now fail before mutating an existing mapping; newly authored outputs are enabled consistently and changed input controls receive detached current axis settings.
- Added nine focused Core tests for Xbox, mouse, keyboard, bipolar PWM, cleanup, deep-copy, preservation, deletion, and transactional failure behavior.
- Verified 320 passing tests, 57.31% merged runtime coverage (`14,752/25,739`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Editor Target Command Ownership

- Moved Xbox, mouse, and visual-key target command instances and parameter handling into `MappingEditorSelectionViewModel`.
- Preserved XAML bindings through compatibility proxies and retained duplicate-warning/status handling behind a typed callback.
- Verified 311 passing tests, 57.50% merged runtime coverage (`14,545/25,295`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Input Learn Presentation Ownership

- Added `InputLearnViewModel` for Learn Mode listening/candidate state, commands, scope text, signal detection, and control highlighting.
- Kept final mapping creation in the shell behind a typed confirmation result and preserved existing XAML through compatibility proxies.
- Added invalid-signal and case-insensitive device-scope regressions.
- Verified 311 passing tests, 57.50% merged runtime coverage (`14,545/25,295`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Axis Curves Page Ownership

- Added `AxisCurveEditorViewModel` as the owner of axis targets, selection, live values, editing defaults, reset, save, and target rebuilding.
- Added UI-independent `AxisCurveProfileEditor` rules for profile persistence, matching-mapping synchronization, saved/mapping/default lookup, range defaults, summaries, and deep copies.
- Removed two mutable fields and the Axis Curves helper implementations from `MainViewModel` while preserving commands and XAML through compatibility proxies.
- Added five focused Core tests covering save synchronization, lookup precedence, mapping fallback, trigger defaults, and deep-copy isolation.
- Verified 309 passing tests, 57.50% merged runtime coverage (`14,545/25,295`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Devices Page Presentation Ownership

- Added `DeviceBrowserViewModel` as the owner of the device collection view, search, physical/virtual/status filter, input-source selection, demo visibility, and visible count.
- Removed four mutable Devices page fields plus WPF collection-view construction and filtering logic from `MainViewModel`.
- Preserved discovery, provider correlation, profile membership, input monitoring, XAML bindings, and workspace grid behavior through existing coordinators and compatibility proxies.
- Kept native device notifications, Container IDs, and expanded HID parsing coverage as separate technical-debt items.
- Verified 304 passing tests, 57.52% merged runtime coverage (`14,437/25,101`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Editor Keyboard State Ownership

- Moved primary and negative keyboard assignments, visual modifiers, selected-key lookup, assigned-key summaries, and live held-key overlays into `MappingEditorSelectionViewModel`.
- Moved keyboard mapping load/parsing and visual-key selection behavior out of `MainViewModel` while preserving existing mapping configuration and Analog PWM behavior.
- Kept the visual keyboard, capture dialogs, duplicate warnings, commands, and XAML bindings unchanged through compatibility proxies.
- Removed five mutable keyboard presentation fields and two mapping parsing helpers from `MainViewModel`; TD-002 remains in progress for commands and other page state.
- Verified 304 passing tests, 57.52% merged runtime coverage (`14,437/25,101`), clean Debug and Release builds, WPF startup smoke, and an architecture report with no findings.

### Backlog Milestone - Mapping Editor Selection Ownership

- Added `MappingEditorSelectionViewModel` as the focused owner of selected device/control, output mode, Xbox/mouse target, hat options, pointer settings, easy preset, and mapping behavior.
- Preserved every existing MainWindow binding through temporary MainViewModel compatibility proxies, avoiding Mapping Editor layout or profile-schema changes.
- Moved default input/output selection rules into UI-independent `MappingSelectionPolicy` with direct axis, button, hat, encoder, and D-pad coverage.
- Removed eleven Mapping Editor selection fields and their transition logic from `MainViewModel`; TD-002 remains in progress for page commands and remaining presentation state.
- Verified 304 passing tests, 57.52% merged runtime coverage (`14,437/25,101`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Runtime Session Coordination

- Added `IRuntimeSessionCoordinator` and a Core implementation that serializes mapping/output start and stop transitions.
- Moved session cancellation ownership, output connection, macro/script lifecycle hooks, queue drain, neutralization, mapping reset, disconnect, and failed-start rollback out of `MainViewModel`.
- Emergency reset now executes output neutralize/reset/disconnect even when no session is active, with isolated cleanup steps and fault diagnostics.
- Added focused sequencing and rollback coverage; TD-002 remains in progress only for page-specific presentation state and smaller UI command coordinators.
- Verified 294 passing tests, 57.53% merged runtime coverage (`14,418/25,063`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Signal-Native Mapping Boundary

- Removed the test-only `IMappingEngine.ApplyEvent` and `ApplySignal` compatibility API; `ProcessSignal` is now the sole mapping execution contract.
- Migrated Core and integration regression tests to RuntimeSignal processing and immutable OutputAction reduction, matching the production path.
- Retained `InputEvent` only as an input-provider acquisition DTO and retained `XboxState` only as Xbox backend/diagnostic state.
- Closed TD-003 without changing profile schema, mapping semantics, output plugins, or user workflows.
- Verified 289 passing tests, 57.72% merged runtime coverage (`14,254/24,695`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Runtime Mapping Coordination

- Extracted runtime profile/device context, held-control state, active-layer resolution, RuntimeSignal scheduling, OutputAction dispatch, drain, and reset behind `IRuntimeMappingCoordinator`.
- Preserved macro/script continuation hooks, live mapping transitions, UI signal sampling, output lifecycle, Safe Mode, emergency reset, and device-recovery behavior.
- Added lock-protected read-only runtime snapshots so macro and UI paths no longer enumerate a mapping-lane-owned mutable held-control set.
- Added focused coverage for inactive gating, mapping-to-output queue flow, continuation publication, shift-layer resolution, and reset cleanup.
- Verified 289 passing tests, 57.75% merged runtime coverage (`14,272/24,713`), clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Device Coordination

- Extracted discovery serialization, simulation filtering, reconnect identity reconciliation, and profile-device mutations behind `IDeviceCoordinator`.
- Moved stable-identity add, enable, disable, and remove behavior out of `MainViewModel` without changing profile schema or mapping preservation.
- Preserved existing HID, Raw Input, simulation, hot-plug, input-only monitoring, and output activation behavior.
- Added direct coverage for simulation visibility, identity migration, deduplication, and case-insensitive membership changes.
- Verified 286 passing tests, clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Input Monitoring Coordination

- Extracted selected-device input monitoring behind the Core `IInputMonitoringCoordinator` contract.
- Centralized equivalent-selection deduplication, provider restart, cancellation, and watchdog recovery outside `MainViewModel`.
- Preserved input-only monitoring: editing, Learn Mode, curves, and live diagnostics still work without starting Xbox, keyboard, or mouse outputs.
- Added direct tests for unchanged selections, changed selections, forced recovery, and empty-device shutdown.
- Verified 283 passing tests, clean Debug and Release builds, and an architecture report with no findings.

### Backlog Milestone - Profile Persistence Coordination

- Extracted profile save-state tracking behind `IProfilePersistenceCoordinator` as the first incremental `MainViewModel` coordinator boundary.
- Added content-based fingerprints that detect nested device, mapping, transform, macro, output, and metadata changes.
- Auto Save now skips unchanged intervals while manual Save continues to force a persisted revision.
- Import, duplicate, rename, First Run, delete replacement, and loaded-profile workflows establish accurate accepted baselines.
- Added direct integration coverage for unchanged, nested-change, manual-save, and rejected-save behavior.

### Backlog Milestone - Dependency Injection Foundation

- Added a dedicated Microsoft DI composition root for the application service graph.
- Reduced `App.xaml.cs` to WPF lifecycle, recovery, watchdog, and coordinated-shutdown orchestration.
- Preserved explicit startup order and existing runtime-service shutdown ownership.
- Reconciled input-only monitoring and Mouse Output as completed backlog work.
- Verified 277 passing tests, a clean architecture report, and isolated fresh/existing/Safe Mode WPF startup.

### Backlog Milestone - Direct Service Coverage

- Added direct behavioral coverage for runtime mapping-state reconciliation and typed runtime variables.
- Added non-destructive Windows boundary coverage for HID and Raw Input enumeration, empty HID-input lifecycle, and ViGEmBus status/unconfirmed installation.
- Kept hardware-specific signal validation in the manual compatibility matrix; automated tests require no particular controller and never launch a driver installer.
- Resolved all six `ARCH006` findings; architecture validation now reports 0 errors, 0 warnings, and 0 suggestions.
- The complete suite now contains 277 passing tests, with clean Debug and Release builds.

### Backlog Milestone - App Composition Cleanup

- Added a stable RuntimeSignal playback interface and supplied its implementation from `App.xaml.cs`.
- Supplied a fresh mapping-engine factory to the Test Runner so benchmarks remain isolated from the live engine without constructing services in the ViewModel.
- Added a keyboard-capture session interface and supplied session creation from the composition root while preserving per-dialog hook disposal.
- Removed all three `ARCH007` findings without policy exceptions; architecture validation now reports 0 errors, 0 warnings, and 6 suggestions.
- Verified 271 passing tests plus clean Debug and Release builds.

### Backlog Milestone - Keyboard Visualization And Composition Boundaries

- Confirmed the existing visual keyboard already supports direct key selection, assigned/conflict/live states, modifiers, and mapping-detail hover text; reconciled the stale backlog status.
- Moved the HOTASBridge injected-input marker to a neutral Core contract shared by output injectors and keyboard capture.
- Removed the Debug Test Runner's Input implementation reference by supplying simulation-catalog metadata from `App.xaml.cs`.
- Reduced architecture validation from two warnings to zero warnings without adding policy exceptions.
- Verified 271 passing tests plus clean Debug and Release builds.

### Backlog Milestone - Guided Analog PWM Mapping

- Added a guided Analog PWM panel for eligible axis-to-keyboard mappings without introducing a second mapping model.
- Added Balanced, Responsive, and Smooth presets plus direct controls for cycle timing, thresholds, duty floor, response curve, smoothing, inversion, and bipolar direction keys.
- Added Core-owned typed configuration, validation, application, removal, and preset logic while preserving existing profile and runtime contracts.
- Added focused tests for presets, validation, transform preservation, bipolar safety, and round-trip configuration; the complete suite now contains 271 passing tests.
- Verified Debug and Release builds, architecture validation, and an isolated Release Safe Mode startup smoke test.

### Backlog Milestone - Visual Macro Editor

- Added an Advanced/Beta Macro Editor over the existing profile-owned macro and runtime-variable model.
- Added safe create, deep duplicate, confirmed delete, condition/action editing, ordering, repeat policy, validation, and friendly target selection.
- New macros are disabled by default and runtime configuration refresh is debounced while mapping is active.
- Added focused core editor-operation tests; the complete suite now contains 267 passing tests.
- Kept macro libraries, import/export, AI generation, and scripting integration deferred.

### Backlog Milestone - Process-Aware Profiles And License Notices

- Added an opt-in local running-application catalog and deterministic profile activation policy.
- Added executable browsing, running-process selection, exact-path preference, duplicate-match conflict reporting, and Safe Mode suppression.
- Kept process inspection local and limited to process identity, start time, and executable path when available.
- Added About to Easy Mode and an internal offline viewer for bundled ViGEmBus BSD-3-Clause and ViGEm.Client MIT notices.
- Added core policy and Windows catalog tests; Debug and Release validation are recorded in the milestone report.

### Chapter 25 - Long-Term Vision and Project Governance

- Added a living long-term product and architecture vision centered on universal HID signal processing.
- Defined stable runtime foundations, extension tests, governance ownership, decision classes, technical-debt policy, and documentation policy.
- Added accepted ADRs for preserving runtime foundations and formal milestone review gates.
- Added a formal review policy, reusable template, retrospective baseline review, and machine-readable schedule.
- Added validator and negative self-test coverage for required review evidence, due manual gates, and profile schema v6 protection.
- Integrated review status into release validation and Project Health while keeping Version 1.0 acceptance explicitly pending.
- Advanced the development version to `0.25.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.

### Chapter 24 - AI Assistant and Intelligent Workflow Foundation

- Added immutable AI evidence, suggestion, and explanation contracts in Core.
- Added a deterministic offline provider that has no mutation, network, hardware, output, or persistence capability.
- Added AI Explain to Device Inspector, Mapping Editor, Signal Flow, Profile Health, and Output Monitor.
- Added a reusable explanation dialog with provider/evidence timestamp, runtime state, transforms, suggestions, and Copy Explanation.
- Added 11 tests for grounding, privacy boundaries, approval policy, missing evidence, profile/output diagnostics, text reports, and feature policy.
- Documented privacy, integration, approval, and deferred remote/generative capabilities.
- Advanced the development version to `0.24.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.

### Chapter 23 - Coding Standards and Architecture Validation

- Added contribution, code-style, review, dependency, breaking-change, and Definition of Done guidance.
- Added an ADR process and reusable decision template.
- Added a versioned architecture policy and BCL-only validator with text/JSON output and configurable CI thresholds.
- Enforced project reference direction, cycle prevention, WPF isolation, solution membership, and valid project metadata.
- Added incremental public API documentation, implementation namespace, service construction, and likely test-gap checks.
- Added nine validator regression tests and integrated architecture JSON into release validation.
- Documented and retained existing warning/suggestion findings as technical debt instead of rewriting working runtime behavior.
- Advanced the development version to `0.23.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.

### Chapter 22 - Installer, Deployment, Updates, and First Run

- Added a compiled Inno Setup source for per-user/machine-wide install, stable upgrade/repair identity, pre-upgrade backup, and preserve-by-default uninstall choices.
- Added a nine-step First Run Setup over normalized input, profile, mapping, and output services.
- Unified ViGEmBus status/install logic and removed the blocking startup prompt.
- Added settings schema v3 for first-run state, update channel, and last update check.
- Added versioned deployment ZIP backup/restore with archive path-containment checks.
- Added installer build, upgrade-backup, and rollback scripts.
- Added an offline update service and policy requiring explicit confirmation and signed packages.
- Added eight Chapter 22 tests; the complete suite now contains 188 passing tests.
- Advanced the development version to `0.22.0-dev`; profile schema remains v6, plugin API remains 1.0, and Script API remains 1.0.
- Deferred portable mode, hosted updates, signing automation, and clean-machine acceptance.

### Chapter 21 - Profile Library, Importers, and Comparison

- Added searchable profile organization by name, game, aircraft, vehicle, hardware, tags, author, and category.
- Added schema-v6 optional category, difficulty, game, aircraft, vehicle, and hardware metadata plus additive v5 migration.
- Added UI-independent importer/exporter/library contracts and JSON providers for legacy profiles and versioned packages.
- Added pre-import summary and compatibility reporting for schemas, plugins, devices, transforms, conversion warnings, and validation errors.
- Added device-neutral templates, selected-mapping packages, device-group packages, and replacement backups.
- Added deterministic profile comparison, selected merge, and JSON/HTML report export.
- Added eight Chapter 21 tests; the complete suite now contains 180 passing tests.
- Advanced the development version to `0.21.0-dev` and profile schema to v6.
- Deferred vendor-specific importers, screenshots, signed packages, cloud/community services, ratings, and repository integrations.

### Chapter 20 - Scripting Engine

- Added the independently versioned `HOTASBridge.ScriptApi` assembly with stable signal, variable, output, profile, scheduler, notification, logger, catalog, lifecycle, and diagnostics contracts.
- Added an optional Lua runtime with top-level discovery, metadata/API validation, event callbacks, typed variables, OutputAction requests, centralized timers, and immutable diagnostics.
- Added a constrained host surface without file, OS, debug, dynamic-load, network, native, or automatic .NET interop capabilities.
- Added bounded instruction slices, transactional side effects, timer cleanup, and per-script compile/runtime failure isolation.
- Added eight scripting regression tests; the complete suite now contains 172 passing tests.
- Added scripting, public API, security, diagnostics, feature-policy, roadmap, debt, and completion documentation.
- Advanced the development version to `0.20.0-dev`; profile schema remains v5, settings schema remains v2, plugin API remains 1.0, and Script API is 1.0.
- Deferred visual editing/debugging, alternate languages, persistent variables, permissions, signed packages, per-script heap quotas, and hostile-code isolation.
### Chapter 19 - Macro Engine and Automation

- Added an optional scheduler-driven Macro Engine over RuntimeSignals, OutputActions, host commands, telemetry, and logging interfaces.
- Added control, timer, profile, and device triggers; profile/layer/variable/device/toggle/time/macro/plugin conditions; and Xbox, keyboard, delay, variable, mapping, profile, and notification actions.
- Added Boolean, Integer, and Float runtime variables whose current state is never persisted.
- Added deterministic delays/repeats, pause/resume, restart, single-step, cancellation, maximum-iteration safeguards, and automatic output release.
- Added a Beta Macro Debugger and Macro stage diagnostics consumed through shared immutable snapshots.
- Advanced profiles additively to schema v5 with `macros` and `macroVariables`, preserving v1-v4 migration.
- Added eleven Chapter 19 tests; the complete suite now contains 164 passing tests.
- Added Macro Engine, macro configuration, runtime variable, migration, schema, navigation, diagnostics, and completion documentation.
- Advanced the development version to `0.19.0-dev`.
- Deferred visual macro authoring, voice/network/SimConnect triggers, random delay, sound, persisted variables, libraries, and scripting integration.

### Chapter 18 - Visual Node Editor

- Added an immutable, UI-independent graph projection for every existing `InputMapping`, including deterministic input, transform, output, and connection IDs.
- Added graph validation for mapping definitions, duplicate identities, missing endpoints, incompatible signals, cycles, missing paths, and duplicate profile outputs.
- Added bounded undo/redo, cross-mapping deep clipboard, multi-node edit operations, device-neutral graph templates, and compatible transform insertion.
- Added a Beta WPF graph workspace with search, zoom, pan, drag, multi-select, minimap, properties, validation, templates, and keyboard commands.
- Added live input, transform, output, timing, activity, warning, error, and timestamp overlays using the existing signal cache and telemetry service.
- Added bidirectional synchronization through the one profile-owned `InputMapping`; no graph copy, profile migration, or runtime bypass was introduced.
- Added eleven Chapter 18 graph tests and one updated feature-policy test; the complete suite now contains 153 passing tests.
- Added Node Editor, graph model, and completion documentation and advanced the development version to `0.18.0-dev`.
- Kept branching, logic/plugin/custom nodes, persisted positions, direct connection drawing, and large-graph virtualization explicitly deferred.

### Compact Dashboard and Mapping Panels

- Standardized Recent Raw Input, Dashboard Xbox state, and Mapping Editor Xbox target at 450 x 220 design dimensions.
- Removed the Dashboard-level scroll viewer and gave Recent Raw Input its own vertical scrolling and wrapped event text.
- Aligned the Dashboard raw-input and Xbox visual top edges and moved driver status below the Xbox visual.
- Set the selected-input editor to 250 px wide with a 375 px minimum height.
- Added down-only scaling for the Mapping Editor Xbox target when docked panels leave less than its nominal width, preventing overlap without introducing a horizontal layout cycle.

### Mapping Editor Responsiveness Fix

- Removed the Mapping Editor's horizontal `ScrollViewer` and forced child widths that caused a circular WPF measure/arrange loop with input-card `ActualWidth` bindings.
- Restored the last responsive `3:2:3` workspace grid while retaining dashboard sizing and Xbox face-button placement improvements.
- Reduced measured five-second Mapping Editor CPU consumption by 76% in an isolated copied-profile Safe Mode smoke test.

### Chapter 17 - Final Validation and Project Health

- Added a stable Project Health page for architecture, documentation, measured coverage, hardware evidence, known issues, active debt, manual acceptance, and live feature flags.
- Added a versioned UI-independent health model and fail-safe JSON provider; invalid or missing evidence produces a blocked report without preventing startup.
- Added release validation for restore, Debug build, 142 automated tests, merged line coverage, Release build, required documents, schemas, executable, embedded report, and ViGEm prerequisite.
- Added coverage collection to the Core test project and merged reports by unique source line.
- Added User Guide, Developer Guide, release notes, Project Health documentation, and final-acceptance procedures.
- Advanced the development version to `0.17.0-dev` while preserving profile schema v4, settings schema v2, and plugin API 1.0.
- Reconciled completed scheduler, hardware-report, plugin, keyboard, and recording technical-debt foundations.
- Kept Version 1.0 explicitly blocked on physical hardware sign-off, installer/signing, clean-machine migration, production runtime policy, and soak evidence.

### Stabilization - Dashboard and Mapping Xbox layout

- Fixed the Dashboard Xbox visual at 450 x 360 and capped Recent Raw Input at 600 px.
- Aligned the Recent Raw Input and Generated Xbox State sections and moved virtual-controller status directly below the Xbox heading.
- Matched the Mapping Editor Xbox surface to 450 x 360 and protected the Selected Input card with a 308 x 375 minimum.
- Shifted the ABXY diamond half a button left and one quarter button upward to clear the right trigger.
- Added bounded scrolling when workspace docks reduce the available document area below these stable dimensions.

### Chapter 16 - Roadmap, Versioning and Release Strategy

- Added a UI-independent feature policy with Stable, Beta, Experimental, DebugOnly, and Hidden stages plus stable persisted keys.
- Added application settings schema v2 for explicit feature overrides and the Release experimental developer opt-in.
- Gated Keyboard Output, Analog PWM, external plugin discovery, Signal Flow, recording/playback, and Debug tools at real startup/runtime boundaries.
- Added a Developer settings view for effective state, build restrictions, and pending-restart status.
- Centralized the development version as `0.16.0-dev`, embedded Git provenance, and added build validation for Development/Beta/Stable channels.
- Added the product roadmap, release process, versioning contract, feature flag guide, and Chapter 16 completion report.
- Added seven Chapter 16 tests; the complete suite now contains 137 passing tests.
- Preserved profile schema v4 and compile-time Release exclusion for Debug dashboard, profiler, and Test Runner sources.

### Chapter 15 - Plugin SDK and Future Expansion

- Added a UI-independent plugin SDK model with manifest schema v1, API v1.0 negotiation, semantic versions, categories, dependencies, origin, signatures, compatibility, and lifecycle state.
- Added a shared plugin catalog that isolates invalid manifests, detects missing/incompatible/cyclic dependencies, and applies Internal/Bundled/External duplicate precedence.
- Added bounded startup discovery for local `plugin.json` files under the dedicated Plugins directory without loading external assemblies.
- Added SDK manifests to the existing Xbox and Keyboard output plugins and connected Output Manager lifecycle transitions to catalog diagnostics.
- Added Plugin Manager installed search and replaceable repository, package, and signature interfaces with explicit offline/read-only Version 2 implementations.
- Added a machine-readable plugin manifest JSON Schema and SDK, API, lifecycle, and Plugin Manager documentation.
- Added ten Chapter 15 tests; the complete suite now contains 130 passing tests.
- Kept external binary loading, signature enforcement, sandboxing, and every online service explicitly deferred.

### Chapter 14 - Testing, Simulation and Quality Assurance

- Added five selectable simulation scenarios: Generic HOTAS, Twin Engine Aircraft, Helicopter, Racing Wheel, and Gamepad.
- Added mixed, seeded-random, and scripted simulation modes while preserving the existing default demo behavior.
- Added versioned RuntimeSignal recording, atomic JSON persistence, timed/deterministic playback, replay quality flags, and OutputAction regression comparison.
- Routed Debug playback through the same live mapping/output path used by hardware signals.
- Added a Debug-only Test Runner for compiled test summary, hardware, profile, output plugin, scheduler, and performance validation.
- Added Test Runner JSON/HTML export and signal recording/session replay controls; all Test Runner sources are excluded from Release.
- Added dedicated testing, simulation, recording format, hardware matrix, and manual validation documentation.
- Added nine Chapter 14 tests; the complete suite now contains 120 passing tests.

### Stabilization - disabled actions and Xbox visual spacing

- Replaced native disabled button chrome with a shared theme-aware template so unavailable actions retain readable muted labels and outlines across Device Inspector, Mapping Explorer, Mapping Editor, Profiles, Settings, Transform Editor, and Performance Profiler.
- Added readable dark tooltips, including tooltip display for disabled controls, so icon-only actions remain identifiable while unavailable.
- Moved Back/Start upward, the face-button diamond rightward, and the D-pad downward in the Xbox visual to reduce overlap with both sticks.

### Chapter 13 - Reliability, Recovery and Fault Tolerance

- Added shared error classification and subsystem health state with `Running`, `Warning`, `Error`, `Disabled`, and `Restarting` transitions.
- Added a Runtime Watchdog for input, mapping queues, output scheduler, output plugins, and queue pressure; monitoring remains independent from mapping and output execution.
- Added isolated automatic output-plugin recovery with timer cancellation and guarded stop/reset/start while healthy plugins continue.
- Added global Emergency Reset in the main window, Output Monitor, system tray, and Debug Developer Dashboard.
- Added Safe Mode through `--safe-mode` and interrupted-session recovery; output plugins and scheduled output remain disabled while profiles and diagnostics load.
- Added atomic session markers, Windows suspend/resume recovery, privacy-limited structured crash reports, and damaged-session isolation.
- Added seven reliability tests; the complete suite now contains 111 passing tests.

### Stabilization - themed controls and live UI responsiveness

- Corrected Mapping Editor control matching so similarly prefixed buttons such as B-3, B-3-7, and B-3-8 remain distinct, while encoder direction suffixes still resolve to their owning encoder.
- Arranged Mapping Editor buttons in two compact columns while keeping axes and other controls full width.

- Replaced native ComboBox chrome with a shared dark-green template so both the selected value and popup items remain readable; selection now uses an outline instead of a white fill.
- Added thinner pill-shaped scrollbars, themed DataGrid headers/select-all/resize grips, and themed workspace splitters.
- Refined the Xbox visualization with centered labels, separated Back/Start controls, and a true face-button diamond.
- Added full axis-pipeline curve preview for calibration, inversion, deadzones, curve, outer saturation, sensitivity, and output range.
- Made device selection a one-click committed checkbox and protected the mapping workspace with proportional columns and bounded dock sizes.
- Moved live visual sampling to render priority, kept Output Monitor at 30 FPS, and limited Signal Flow refresh to its visible page.
- Removed high-frequency ObservableCollection churn by incrementally filtering Device Inspector events, recording history only while visible, throttling analog history per control, and sampling the Dashboard feed.
- Verified active demo mapping at about 62.6 Xbox updates/sec with 12/12 changing UI samples, zero Axis Curves horizontal scrollbars, successful navigation across diagnostics pages, and coordinated shutdown.
- The complete suite contains 104 passing tests.

### Chapter 12 - Runtime Scheduler, Threading and Performance

- Added a central runtime work scheduler with bounded single-reader lanes for runtime signals, output actions, diagnostics, input events, and future background work.
- Moved live mapping and output dispatch off WPF callbacks while coalescing visual updates to a 33 ms UI sampler.
- Added queue depth, peak, rejected count, average wait, average processing, health, and timed-output average/worst latency telemetry.
- Serialized live mapping rebuilds against evaluation to protect transform and runtime state during editing.
- Replaced synchronous JSON logging with a bounded asynchronous writer and deterministic shutdown flush.
- Replaced the unreliable asynchronous `OnExit` cleanup with coordinated window-closing shutdown that drains runtime work before WPF exits.
- Added a Debug-only Runtime Performance Profiler with recording, atomic session save/load, and two-session comparison; all profiler sources are excluded from Release.
- Added scheduler overflow/drain and logging flush regression tests.

### Chapter 11 - WPF User Interface and User Experience

- Added a native WPF dockable workspace with resizable left/right/bottom regions, move, hide/show, pin/unpin, named custom layouts, four built-ins, and restore default.
- Added profile-independent atomic workspace JSON persistence under the dedicated `Workspaces` application-data directory.
- Added Light, Dark, and System themes through dynamic resources with persisted application choice.
- Added consistent navigation names/order, dashboard quick actions, a bounded notification history, categorized Settings, and an always-visible runtime status bar.
- Added Devices search/filter and add/enable/disable/remove actions while preserving mappings on device removal.
- Added Mapping Explorer Delete/Delete-key and one-step Undo for non-destructive bulk management.
- Added workspace/settings persistence tests and isolated WPF UI Automation for workspace, mapping, device, and startup workflows.
### Stabilization - Startup and mapping persistence

- Removed the Developer Dashboard runtime `git.exe` process launch; Debug builds now read branch and commit metadata directly from repository files.
- Corrected the Mappings grid Delete action to remove the selected mapping from the active profile and runtime mapping set, rather than only removing its visible row.
- Added an explicit Delete button and retained Delete-key operation while disabling WPF's non-persistent automatic row deletion.
- Added regression coverage proving removed duplicate mappings stay removed after profile save and reload.

### Chapter 10 - Keyboard Output System and Analog PWM

- Added complete mapping-owned PWM timing, duty limits, activation/release and full-hold hysteresis, smoothing, curve, and inversion settings.
- Added bipolar PWM with separately captured positive/negative shortcuts and scan codes, center release, and release-before-press switching.
- Added scan-code-aware SendInput events with a HOTASBridge injection marker and a physical-key capture hook that filters injected events.
- Added Xbox/Keyboard mapping-editor output modes, keyboard capture, clear/retry, modifier combinations, and active-profile duplicate warnings.
- Corrected keyboard repeat mappings to emit repeat actions instead of PWM actions.
- Added per-mapping PWM duty/state/key/timer/latency/pulse diagnostics for Output Monitor and Signal Flow telemetry.
- Added five Chapter 10 tests; the complete suite now contains 93 passing tests.
### Chapter 9 - Output System Architecture

- Added the common `IOutputPlugin` lifecycle, health, reset, diagnostics, and action-batch contract.
- Preserved the existing ViGEm Xbox implementation behind `XboxOutputPlugin` and retained one backend update per action batch.
- Added Windows SendInput keyboard output with key combinations, reference-counted holds, toggle, pulse, repeat, PWM, and clean shutdown release.
- Added one centralized `OutputScheduler` loop for delayed, repeating, PWM, and future timed output work.
- Refactored Output Manager for plugin loading/ownership, isolated failures, per-plugin reset, runtime snapshots, and telemetry.
- Added the manager-backed Output Monitor with Xbox and Keyboard cards, rates, queue depth, state, errors, and reset controls.
- Added six Chapter 9 integration tests; the complete suite now contains 87 passing tests.

### Chapter 8 - Transform Engine

- Added a registered, RuntimeSignal-native Transform Engine with independently testable built-ins and isolated per-mapping state.
- Added schema v4 per-device/control calibration and ordered standard transform chains while preserving older profiles and legacy mappings.
- Added moving-average, exponential-smoothing, median, logarithmic/custom curves, scaling, inversion, clamp, split, threshold, toggle, pulse, analog PWM, and direction transforms.
- Added per-transform shared telemetry for input/output values, duration, state, warnings, errors, and last execution time.
- Added affected-mapping live rebuilds and the Transform Editor for chain ordering, enable/disable, arbitrary settings, and live apply.
- Added versioned device-independent Transform Presets with save/load/rename/duplicate/delete/import/export.
- Added eleven Chapter 8 tests; the complete suite now contains 81 passing tests.
### Chapter 7 - Mapping Engine

- Added schema v3 mapping names, priorities, notes, generic output targets/configuration, conditions, and profile conflict policy.
- Refactored the Mapping Engine to indexed RuntimeSignal lookup and immutable OutputAction batches while retaining tested Xbox compatibility adapters.
- Added profile/layer/held-control/device/toggle conditions and ordered descriptor-driven axis transforms.
- Added runtime output contributions with deterministic First Wins, Last Wins, and Highest Priority conflict resolution across separate events.
- Added immediate release/transfer actions for live disable, removal, retargeting, layer changes, and failed conditions.
- Added an Output Manager that routes Xbox actions to ViGEm and diagnoses unloaded keyboard/PWM/generic backends.
- Added the searchable, sortable, groupable Mapping Explorer with multi-select, duplicate, enable/disable, CSV export, runtime status, and direct editor updates.
- Added fifteen Chapter 7 tests; the complete suite now contains 70 passing tests.

### Chapter 6 - Profiles, Persistence, and Configuration

- Added schema v2 profile metadata, logical device groups, output plugin configuration, and independent transform descriptors while preserving existing runtime fields.
- Added backup-preserving v1 migration, newer-schema downgrade protection, null-field hardening, validation, and atomic JSON saves.
- Added Import, Export, Duplicate, Rename, Save As, built-in templates, recent profiles, and configurable Auto Save.
- Separated profiles, application settings, backups, templates, logs, diagnostics, and temporary storage.
- Added the automatic Profile Health Report with issue navigation for missing devices, duplicates, conflicts, plugin state, and schema status.
- Added a machine-readable JSON Schema and Chapter 6 profile/migration documentation.
- Added eleven profile-system test cases; the complete suite now contains 55 passing tests.

### Chapter 5 - Device Inspector and Live Diagnostics

- Extended the cache-backed Device Inspector with summary, axis output/quality, performance, mapping preview, and unknown-control visibility.
- Added typed, filterable per-device event history and UI-only freeze/reset controls.
- Added UI-independent JSON, CSV, and text diagnostics export.
- Added source/control/pipeline/order metadata to shared runtime stage diagnostics.
- Added the live/freeze Signal Flow Inspector over shared cache and telemetry snapshots.
- Added five diagnostics/export and telemetry-flow tests.

- Chapter 1 assessment documents added.
- Debug-only Developer Dashboard added for Agent Note 001 diagnostics.
- Chapter 2 architecture document expanded with runtime flow, thread ownership, dependency, startup, and shutdown diagrams.
- RuntimeSignal foundation added and mapping engine now consumes signals through `IMappingEngine`.
- UI-independent telemetry and runtime stage diagnostics framework added for Developer Dashboard and future Signal Flow Inspector.
- Chapter 3 expanded RuntimeSignal with raw/normalized/current/previous values, state, required signal types, quality, metadata, diagnostics, flags, and read-only publication.
- Added RuntimeSignalEngine, signal publication events, and an engine-owned latest-value Runtime Signal Cache.
- Added deterministic insertable stages for normalization, calibration, deadzone, filtering, response curve, scaling, transform, and output mapping.
- Moved toggle, pulse, current/previous value, and timer state into a runtime-only mapping state store.
- Device inspector, curve monitor, learning, diagnostics, and mapping now consume the same published RuntimeSignal per input.
- Chapter 4 added the signal-native `IInputProvider` boundary and provider status/error/lifecycle contracts.
- Existing Windows HID, Raw Input, and simulation implementations are wrapped by `InputProviderAdapter`.
- `CompositeInputProvider` now deduplicates providers, monitors hot-plug/removal, reports health, and publishes telemetry.
- The app and MainViewModel now consume only the common input provider interface.
- Device identity matching now prioritizes Stable ID, internal GUID, Container ID, serial, path, VID/PID, usages, and name.
- Connected Devices now shows provider, manufacturer, and health.
- Input Learn Mode now supports selected scope, noise baselines, first-input highlighting/details, Confirm, Retry, and input-only capture.
- Added Chapter 4 input-layer, device-model, discovery, tests, and completion documentation.

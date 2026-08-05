# Changelog

All notable release-facing HOTASBridge changes are recorded here. Detailed chapter history remains in [docs/CHANGELOG.md](docs/CHANGELOG.md).

## Unreleased
### Added
- Added an optional Beta Xbox-family output backend through HIDMaestro 1.4.3. Existing `xbox360` mappings route to the profile-selected Xbox backend without profile migration; ViGEm Xbox 360 remains the default.
- Added explicit UAC-elevated HIDMaestro install/repair from Output Monitor, local third-party license notices, mutually exclusive Xbox backend selection, and hardware-independent conversion/routing regressions. No driver is installed silently.
- Added a profile-owned, stopped-runtime HIDMaestro identity selector for official Xbox 360, One, One S, Series X|S, Elite, and Adaptive profiles; active Xbox backend checkboxes are mutually exclusive and the inactive backend is grayed out.
- Added repeatable unsigned Development-channel packaging with explicit assembly metadata, dev.N artifact names, a framework-dependent ZIP, manifest/checksum verification, and non-latest GitHub release guidance.
- Added profile-owned head tracking with an extensible provider boundary, OpenTrack/LookPilot input, Hold/Toggle HOTAS activation learning, recenter and tracking-loss recovery, live diagnostics, and selectable pointer or simulated mouse movement through the existing Mouse Output plugin.
- Added LookPilot compatibility through both its official `opentrack` UDP output and native FreeTrack shared memory, including dedicated provider choices, setup guidance, stale-source handling, and source-labelled diagnostics.
- Added native TrackIR input through the installed NaturalPoint NPClient interface, including signature validation, six-axis conversion, near-120 Hz polling, stale/mouse-emulation diagnostics, deterministic cleanup, and hardware-free regressions; no SDK or vendor DLL is bundled.
- Head-tracking activation can now apply a configurable 0-2000 ms settling delay before capturing the head center and recentering the Windows pointer on the foreground monitor through the existing Mouse Output plugin.
- Added explicit Absolute Position, Relative Movement, and Velocity head-tracking mouse modes. Absolute Position now maps equal center-to-left and center-to-right motion symmetrically across the foreground monitor without speed-limited target chasing.
- Prevented overlapping mapping starts from failing when they reapply the same active output selection; real output changes still require mapping to stop.
- Added DualShock 4 and DualSense touchpad input over USB and Bluetooth, including two contacts, packed 12-bit positions, contact IDs, movement deltas, lift detection, two-finger state, and the physical touchpad click.
- Added an Easy Mode PlayStation touchpad preset that maps first-finger movement to immediate relative mouse movement and the physical pad click to the left mouse button through the existing Mapping Engine and Mouse Output plugin.
- Replaced the axis-curve side dropdown with plain Negative/Positive columns, one shared curve type, editable three-decimal values, explicit S-curve strength, and linked or independent directional tuning.
- Added interactive custom curve points with click-to-add, drag-to-position, right-click removal, visible three-decimal coordinates, and automatic left/right side selection.
- Added Easy Mode keyboard PWM authoring with left-side Global/Custom selection, Full/Positive/Negative axis ranges, a dedicated persisted Global PWM timing page, and per-key custom editing by right-clicking the visual keyboard.
- Added red key-down and green key-up waveform phases, continuous-hold visualization at 100% duty, and double-click-to-zero preview sliders.
- Advanced application settings additively to schema v9 for persisted Global PWM authoring defaults and the PlayStation touchpad input switch; profiles and existing mappings are unchanged.
- Simplified the custom PWM popup by removing curve, smoothing, inversion, bipolar, and preset controls while retaining existing-profile compatibility in the runtime model.

### Fixed
- Corrected Mapping Editor context-menu and selected axis/hat text contrast, and made the full analog input card open the Curve Editor on right-click.
- Corrected mouse preset rows that were mislabeled as `Xbox: A`; mouse mappings now show their actual pointer action.
- Enabled full DualShock 4 Bluetooth HID reports before parsing touch samples, preventing Windows' padded minimal `01` report from being mistaken for a USB touch report.
- Removed high-frequency HID report/button collection allocations, added a live PlayStation touchpad input switch, and kept diagnostic touch axes out of the normal Mapping Editor control list.
- Restored all ordinary DualShock 4 and DualSense controls after full Bluetooth touch reports are activated by decoding sticks, triggers, D-pad, and the real named buttons from the extended report; supported Sony devices no longer expose descriptor-estimated button floods.
- Made single-finger and two-finger touch modes mutually exclusive, with dedicated two-finger centroid X/Y and pinch-distance signals so a two-finger gesture cannot also drive the normal pointer mapping.
- Mapping Editor and Mapping Explorer additions, edits, enable changes, duplicates, and deletions now save automatically to the affected profile, with pending changes flushed during shutdown.
- Added per-profile Xbox, Keyboard, and Mouse output switches; disabled plugins no longer initialize, receive actions, or create their virtual device when mapping starts.


## 0.28.0 - 2026-08-03

### Added
- Added repeatable unsigned Development-channel packaging with explicit assembly metadata, dev.N artifact names, a framework-dependent ZIP, manifest/checksum verification, and non-latest GitHub release guidance.
- Added an Easy Mode **Split axis to Xbox triggers** preset that turns a centered bipolar axis into independent 0..1 LT/RT outputs, with the selected trigger assigned to the positive direction and both triggers neutral at center.
- Grouped Mapping Editor Quick Presets by Hat / D-pad, Stick axis, Throttle axis, and Buttons & switches in a separate scrollable output-side card.
- Added explicit LT/RT and Left/Right Stick preset targets, plus center-safe Direct/Inverted split-axis direction.
- Separated Mapping Function from Behavior in the normal editor and persisted generated split, threshold, and direction-detection stages independently.
- Added isolated stopped-runtime Mapping Editor previews for Xbox, keyboard, and mouse mappings without starting output plugins or the virtual controller driver; Dashboard and Output Monitor remain actual-output only.
- Replaced the abstract Xbox visual with separate supplied top/front Xbox 360 line art in an enlarged 620x380 surface, close-set leader-line callouts, live stick markers, directional D-pad highlights, corrected bumper/trigger labels, button activity, and lower-control LT/RT analog gauges while preserving click-to-map selection; Output Monitor cards now size appropriately for their content.
- Added an Easy Mode axis-curve shortcut: right-click any Mapping Editor axis to open a focused live Curve Editor with save/reset controls; saved axes display an `S` badge.

- Anchored the Mapping Editor output and Quick Presets column at a constant 14-pixel gap from Selected input; additional window width is now reserved to the right instead of shifting or center-clipping the controller visual.
### Fixed
- Allowed installer repair and in-place overwrite to continue when the optional pre-upgrade backup cannot copy locked application files, with a user-data-only retry and logged fallback to Inno transaction rollback.
- Corrected the stick-axis preset so controls named Y Axis or RY automatically target Xbox Left Stick Y instead of Left Stick X.


## 0.27.0 - 2026-07-30

### Added
- Added repeatable unsigned Development-channel packaging with explicit assembly metadata, dev.N artifact names, a framework-dependent ZIP, manifest/checksum verification, and non-latest GitHub release guidance.
- Restricted Advanced Mode unlocking to Debug builds, clarified its Release status in Settings, separated Easy Macro action fields to prevent overlap, and made the Mapping Editor's Saved mappings area vertically resizable.
- Added explicit certificate/private-key ignore rules and ADR 0006, which keeps vJoy output and driver bundling deferred until a maintained signed upstream passes the supported-Windows acceptance matrix.
- Added a persistent Easy Mode interface lock with a deliberate General Settings unlock, advanced-only settings and Macro Editor gating, and non-destructive relocking.
- Added optional independent negative and positive curve, inner/outer deadzone, and sensitivity settings for bipolar axes while preserving existing symmetric profiles.
- Added device-first axis selection and a stable square live preview to Axis Curves.
- Introduced the user-facing **Elka HOTAS Bridge** product name with a dark teal-green application icon, in-app branding, installer presentation, and GitHub artwork while preserving existing `HOTASBridge` executable, namespace, profile-path, and installer identity compatibility.
- Added a default Easy Macro Editor with familiar ordered action authoring while retaining the existing conditions, variables, and complex triggers in Advanced mode over the same profile model.
- Added bounded physical keyboard/mouse macro recording, injected-event filtering, quick key/click/toggle actions, mouse movement/wheel replay, and versioned single-macro import/export.
- Added live macro recording previews, replayable mapped HOTAS click capture, and one-click trigger learning for HOTAS buttons, keyboard keys, mouse buttons, and modifier chords.
- Added one-to-five-step Sequence and Ping-pong playback, runtime-only cursors, Xbox/keyboard/mouse toggles, profile persistence, diagnostics compatibility, and regression coverage.
- Suppressed physical keyboard and mouse down, repeat, and up events when an active macro presses or toggles the same host control, preventing duplicate first input while preserving different-key triggers.
- Made keyboard toggle and standalone Down actions behave like sustained physical holds, including Windows-rate key repeat through the centralized scheduler and deterministic release on toggle-off, profile change, runtime stop, failure, or reset.

- Added fail-closed certificate-store Authenticode signing for first-party binaries, setup, and uninstaller; versioned SHA-256 release manifests and independent verification; plus a disposable-machine retained-upgrade, rollback, repair, and uninstall acceptance runner without claiming unavailable production evidence.
- Added signed `.hotasbundle` profile packages with Windows CNG local publisher identity, offline trust and provenance checks, bounded PNG/JPEG screenshots, and a conservative Thrustmaster T.A.R.G.E.T. basic `MapKey` importer while preserving legacy JSON and excluding online services.
- Hardened Experimental Lua scripting with deterministic ECDSA-signed packages, a local publisher trust store, explicit per-operation permissions, bounded source/event/command/instruction/string/allocation resources, fail-closed isolation policy, live security diagnostics, and an Advanced Script Workbench for validated atomic local editing and runtime reload.
- Added optional profile-schema-v9 branching mapping graphs with stable typed ports, direct connection editing, deterministic logic nodes, registered internal plugin-node processing, bounded DAG execution, per-node diagnostics, workspace-schema-v3 positions/zoom, viewport culling, and graph-safe migration, duplication, comparison, and merge.
- Promoted enabled mapping behavior, toggle, and pulse descriptors to runtime authority in profile schema v8, with one-time v1-v7 projection migration, behavior/timing signal metadata, focused editor synchronization, and descriptor-free compatibility fallback.
- Added Windows SetupAPI Container ID discovery and additive profile identity backfill, preserving existing Stable IDs and fallback matching when the property is unavailable.
- Added native Windows HID topology notifications with a private message-only window, debounced discovery refresh, a 30-second safety poll, automatic two-second polling fallback, telemetry, and lifecycle regressions.
- Added discoverable Moving Average, Exponential Smoothing, and Median Filter controls in Transform Editor, with centralized defaults, profile validation, live rebuilds, and deterministic regression coverage.
- Added a process-wide typed runtime event bus with ordered thread-safe delivery, disposable subscriptions, per-subscriber fault isolation, delivery counters, and immutable signal/profile/stage/plugin/output messages.
- Replaced basic Device Inspector hat, encoder, and switch rows with live directional grids, pulse-direction counters, and position tracks while preserving Learn Mode and diagnostics metadata.
- Added cumulative ViGEm backend connection, report-submission, and cleanup failure counters, shared telemetry/stage warnings, Output Monitor details, and hardware-independent failure-path regressions.
- Added configurable 1-365 day log retention, bounded 100 ms/128-event JSON-lines batching, retention/write telemetry, and application-settings schema v6 persistence.
- Removed the unreachable XboxOutputLegacy and ProfilesLegacy shell views after verifying canonical navigation uses the focused OutputMonitorView and ProfilesView pages.
- Extracted Settings into SettingsView, preserved all seven categories and persistence bindings, connected Developer feature rows directly to FeatureFlagSettingsViewModel, and removed shell forwarding.
- Extracted Axis Curves into AxisCurvesView, bound all targets/settings/live values/commands directly to AxisCurveEditorViewModel, removed the shell forwarding bridge, and added Advanced Mode smoke coverage.
- Extracted Mapping Editor into MappingEditorView while preserving its finite-width anti-lockup layout, bound collections and Learn Mode directly to focused owners, and removed obsolete shell aliases/forwarding.
- Extracted Device Inspector into DeviceInspectorView, bound tabs directly to ProfileDeviceTabsViewModel, preserved shared Mapping/Learn selection coordination, and added hardware-free Easy Mode smoke coverage.
- Extracted the Dashboard into DashboardView, kept its fixed 450x220 live-input and Xbox panels, bound activity directly to RuntimeActivityViewModel, removed the shell activity alias, and expanded Easy Mode UI smoke coverage.
- Extracted the Devices page into DevicesView, bound presentation state directly to DeviceBrowserViewModel, preserved remembered grid widths/order inside the view, removed obsolete shell aliases, and added Easy Mode UI smoke coverage.
- Extracted the Advanced Diagnostics page into DiagnosticsView, bound the page and workspace dock directly to RuntimeActivityViewModel, removed the shell diagnostics collection proxy, and added Advanced navigation smoke coverage.
- Extracted the Easy/Advanced About page into a focused AboutView while preserving offline ViGEm license access and setup recovery; WPF smoke now verifies both actions through Easy Mode navigation.
- Added one shared Windows HID value-cap parser for discovery and live input, with tested native layout, bounded usage ranges, signed bit-width conversion, hat null preservation, clamping, and exact bipolar axis endpoints.
- Added UI-independent, versioned runtime telemetry sessions, average/comparison analysis, and an atomic JSON history store shared by the Debug Performance Profiler and future diagnostics consumers.
- Completed the TD-002 `MainViewModel` decomposition foundation: Core coordinators own runtime/profile/device workflows, focused view models own page state, and the shell retains explicit lifecycle and cross-page orchestration; remaining XAML proxy removal stays tracked by TD-010.
- Added `FeatureFlagSettingsViewModel` for Settings feature rows, build/summary text, startup comparison, pending-restart state, and override toggles while retaining shell-owned persistence.
- Added `RuntimeActivityViewModel` for bounded Dashboard Recent Events and input Diagnostics feeds, preserving signal throttling, formatting, startup ordering, and retention behavior.
- Extended `DeviceBrowserViewModel` to own discovery row replacement and active-profile membership synchronization while preserving coordinator-owned discovery and profile mutation.
- Added `ProfileCollectionsViewModel` for profile and recent-profile presentation collections, routing startup, CRUD, import, First Run, ordering, deduplication, and retention updates through one owner.
- Added `ProfileDeviceTabsViewModel` for the Device Inspector tabs derived from enabled devices in the active profile while preserving shell-coordinated selection and runtime refresh effects.
- Added `MappingAuthoringViewModel` for Mapping Editor create/update, Easy presets, edit loading, validation, learned/demo mapping creation, and deletion while preserving tested Core profile mutations.
- Added a focused owner for derived mapping rows, selected-device controls, and duplicate-device warnings; WPF smoke now waits for completed startup and shutdown emits per-step diagnostics.
- Added a focused notification center for current shell status, post-initialization history, severity classification, and bounded retention while preserving existing bindings.
- Added focused Mapping Editor and Axis Curves command owners while preserving existing MainWindow bindings and mapping/profile behavior.
- Added a tested Core `ProfileManagementCoordinator`, App profile-dialog boundary, and `ProfileCommandsViewModel` for profile CRUD workflows, last-profile replacement rollback, file selection, and command presentation.
- Added a focused `NavigationViewModel` and tested Core Easy/Advanced navigation policy for feature-filtered destinations, null-safe page selection, mode rebuilds, and shared sidebar/quick-action commands.
- Moved physical keyboard capture and clear commands into `MappingEditorSelectionViewModel`; a new App dialog service now owns WPF window creation and capture-session composition while existing XAML bindings remain compatible.
- Added a tested Core `MappingProfileEditor` for transactional Xbox/keyboard/mouse mapping creation, editing, deletion, output enablement, mutable-settings cloning, PWM configuration, and compatibility synchronization.
- Moved Xbox, mouse, and visual-key target commands into `MappingEditorSelectionViewModel`, leaving the shell only a typed visual-key assignment callback for duplicate-warning and status coordination.
- Added `InputLearnViewModel` for Mapping Editor Learn Mode listening, candidate presentation, retry/cancel/confirm commands, signal detection, and control highlighting while preserving shell-coordinated mapping creation.
- Added `AxisCurveEditorViewModel` for Axis Curves page state and a tested Core `AxisCurveProfileEditor` for profile save, mapping synchronization, settings lookup, defaults, summaries, and deep-copy rules.
- Added `DeviceBrowserViewModel` as the Devices page owner for its collection view, search, physical/virtual/status filter, input-source choice, demo visibility, and visible-device count while preserving Core discovery and profile coordination.
- Moved Mapping Editor keyboard shortcuts, visual modifiers, selected-key lookup, assigned-key summaries, and live held-key overlays into `MappingEditorSelectionViewModel` while preserving the existing visual keyboard and capture workflow.
- Extracted Mapping Editor device/control/output selection into `MappingEditorSelectionViewModel`, with tested Core defaults for axis, hat, encoder, D-pad, Xbox, and mouse selection behavior while preserving existing XAML bindings.
- Added a Core runtime-session coordinator that owns serialized mapping/output startup, cancellation, extension hooks, queue drain, neutralization, reset, disconnect, and failed-start rollback outside `MainViewModel`.
- Completed the signal-native Mapping Engine boundary by removing the unused `ApplyEvent`/`ApplySignal` public adapters and migrating regression tests to `ProcessSignal` plus immutable `OutputAction` batches.
- Added a Core runtime mapping coordinator that owns profile/device context snapshots, held-control and active-layer state, deterministic RuntimeSignal scheduling, OutputAction dispatch, drain/reset behavior, and queue rejection telemetry outside `MainViewModel`.
- Added an input-monitoring coordinator that owns selected-device monitoring, restart deduplication, cancellation, and watchdog recovery outside `MainViewModel`.
- Added a device coordinator for discovery serialization, simulation filtering, profile membership changes, and reconnect identity reconciliation outside `MainViewModel`.
- Added profile persistence coordination with content-based dirty tracking so unchanged Auto Save intervals no longer write files or increment profile revisions.
- Added a Microsoft dependency-injection composition root and reduced `App.xaml.cs` to application lifecycle orchestration while preserving existing runtime ownership and startup order.
- Direct automated coverage for runtime mapping/variable stores, Windows HID and Raw Input discovery boundaries, HID input lifecycle, and non-destructive ViGEmBus driver assessment; architecture validation now has no findings.
- Composition-root injection for RuntimeSignal playback, isolated mapping benchmarks, and physical keyboard-capture sessions; no service construction suggestions remain in the App layer.
- Neutral Core metadata for identifying HOTASBridge-generated Windows input, shared by keyboard/mouse injection and keyboard capture filtering.
- Zero-warning architecture-validation baseline after removing the final App references to concrete Input/Output implementation namespaces outside the composition root.
- Guided Analog PWM authoring for axis-to-keyboard mappings, with Balanced, Responsive, and Smooth presets plus live validation of timing, thresholds, curves, smoothing, inversion, and bipolar direction keys.
- Mapping-owned PWM settings continue to use the existing profile schema, transform pipeline, Keyboard output plugin, and centralized scheduler.
- Beta Visual Macro Editor for creating, duplicating, deleting, validating, and reordering profile-owned macros, conditions, actions, repeat policies, and runtime-variable definitions.
- Friendly device, control, mapping, profile, Xbox, and keyboard selectors for macro triggers and actions, with debounced live runtime refresh and safe disabled-by-default creation.
- Opt-in local process-aware profile activation with executable browsing, a running-process picker, deterministic path/name matching, and conflict-safe behavior.
- Easy Mode access to About plus an in-app offline viewer for the bundled ViGEmBus and ViGEm.Client license notices.

- Easy and Advanced interface modes over the same profiles, mappings, devices, and runtime services.
- Guided output selection, Easy Mode mapping presets, and always-on input testing independent of generated outputs.
- Provider-aware normalized hats for DirectInput, HID zero-/one-based ranges, individual buttons, center press, diagonals, and PlayStation-style D-pads.
- Input-provider correlation, precedence, duplicate warnings, and an Advanced override while preserving distinct virtual devices.
- Mouse output plugin with scheduled hat/axis movement, acceleration, modifiers, buttons, horizontal/vertical wheel, diagnostics, and emergency cleanup.
- Data-driven US ANSI and Nordic ISO visual keyboards plus interactive visual mouse output and accessible list fallbacks.
- Profile schema v7, settings schema v5, migration tests, compatibility schema, ADR, and formal architecture review.

- Living long-term vision and project-governance policy for the universal HID signal-processing platform.
- Formal architecture-review policy, template, baseline review, machine-readable milestone schedule, and accepted foundation ADRs.
- Architecture-review validator, positive/negative self-test, profile-schema protection, and release-readiness integration.

- Offline, read-only AI Assistant contracts, bounded evidence adapters, and a deterministic local advisor.
- AI Explain actions for Device Inspector, Mapping Editor, Signal Flow, Profile Health, and Output Monitor, with copyable support reports.
- Explicit approval, privacy, integration, feature-policy, tests, and Chapter 24 documentation.

- BCL-only Architecture Validator for dependency direction, cycles, WPF isolation, solution membership, public API documentation, implementation leakage, construction, and service-test guidance.
- Versioned `architecture.rules.json` policy, PowerShell runner, JSON report, automated tests, and release-validation integration.
- Contributor guide, code-style standard, ADR process, architecture-validation reference, and Chapter 23 completion documentation.

- Compilable Inno Setup package supporting per-user/machine-wide installation, upgrade backup, repair, and preserve-by-default uninstall choices.
- Nine-step First Run Setup for device detection/selection, driver verification, profile selection, input testing, starter mapping, and Xbox output verification.
- UI-independent deployment prerequisite, shared ViGEmBus driver, backup/restore, update policy, and first-run decision contracts.
- Versioned deployment backup/restore scripts with path-containment protection and an offline Stable/Beta update extension point.
- Installation, deployment, update, rollback, migration, and Chapter 22 completion documentation.

- Local searchable Profile Library with category filters and schema-v6 organization metadata.
- UI-independent importer/exporter contracts, versioned profile/template packages, compatibility preview, and backed-up replacement import.
- Device-neutral template, selected-mapping, and device-group exports.
- Profile Comparison Tool with side-by-side differences, selected merge, and JSON/HTML reports.
- Profile-library, import/export, metadata, schema, migration, and Chapter 21 completion documentation.

- Experimental Lua Scripting Engine with a stable public Script API, constrained host surface, centralized scheduling, instruction limits, transactional side effects, and isolated diagnostics.
- Script discovery, lifecycle, RuntimeSignal/system events, typed variables, OutputAction requests, profile/mapping commands, notifications, logging, and timers.
- Scripting API, security, runtime, diagnostics, and Chapter 20 completion documentation.

- Beta Macro Engine with RuntimeSignal and system-event triggers, conditions, ordered actions, repeat safeguards, and runtime-only variables.
- Central-scheduler macro timers, delays, pause/resume, cancellation, output release, and immutable diagnostics.
- Macro Debugger with live state, variables, scheduler details, errors, and Pause, Resume, Stop, Restart, and Step controls.
- Profile schema v5 with additive `macros` and `macroVariables` configuration plus v1-v4 migration.
- Macro Engine, macro configuration, runtime-variable, and Chapter 19 completion documentation.
- Project Health page and validated machine-readable release-readiness report.
- Beta Visual Node Editor over the existing profile-owned mapping model, with typed graph projection, validation, live diagnostics, templates, history, clipboard, zoom, pan, drag, and minimap.
- Bidirectional synchronization between the Mapping Editor, Transform Editor, Mapping Explorer, runtime mapping engine, and Node Editor without a second profile format.
- Node graph architecture, connection rules, compatibility, and Chapter 18 completion documentation.
- Repeatable restore/build/test/coverage/document/artifact validation command.
- User Guide, Developer Guide, release notes, and final-acceptance documentation.
- UI-independent feature flags with Stable, Beta, Experimental, DebugOnly, and Hidden policy stages.
- Persisted feature overrides and an explicit developer opt-in for Experimental features.
- Product roadmap, release process, versioning contract, and feature-policy documentation.

### Fixed

- Count only unresolved entries as active technical debt in Project Health; completed and intentionally deferred entries remain visible in the report history without degrading the current debt metric.
- Updated WPF release smoke automation for current Elka HOTAS Bridge titles, the locked Easy Mode unlock workflow, navigation rebuilds, and signed profile-package labels.
- Made Project Health coverage validation tolerate only a five-line/0.02-point asynchronous branch drift while retaining exact test-total and total-instrumented-line checks.
- Accept both object-backed and string-backed PowerShell certificate EKU representations so valid code-signing certificates are not rejected during release builds.
- Keep the 1 kHz runtime signal path independent from WPF rendering by sampling only the active page, throttling diagnostics, and avoiding unchanged Xbox-state redraws that caused recurring live-input stalls.
- Render keyboard key selections as concise labels and keep curve selection strongly typed across the editor and live preview.
- Accept built-in mouse outputs during profile validation and expose whole-control mapping presets in both Easy and Advanced modes.
- Start hat-to-mouse pointer movement immediately and recover the shared scheduler registration if its movement job is lost.
- Keep navigation selection null-safe while Easy/Advanced mode changes rebuild the visible page list, preventing a `SelectedPageKey` startup or mode-switch exception.
- Render Quick Preset selections by their friendly names instead of record-debug text.
- Put profile Save beside New on the Profile Library page and show the next step after creating a profile.
- Allow confirmed deletion of any selected profile; deleting the final profile now creates a clean persisted replacement instead of reopening its mappings.

### Changed
- Reconciled release evidence after successful certificate-backed publication, pinned stable .NET SDK 10.0.302 with previews disabled, and separated complete architecture from outstanding manual acceptance gates.
- Improved compact-window usability with scrollable Mapping Editor input, Node Palette, and Profile Health panels; adjusted node diagnostic spacing; and limited X/Y position visuals to meaningful paired axes.
- Force the Inno Setup installer and uninstaller to use the modern dark style, including the title bar, regardless of the Windows light/dark preference.
- Removed the unused legacy `CompositeInputCatalog` and `CompositeInputEventSource`; active per-provider adapters and the signal-native `CompositeInputProvider` remain unchanged.

- Remember main-window placement and Devices-grid column widths/order in workspace schema v2, with stable non-adaptive device columns.
- Advanced the development product version to `0.26.0-dev`; profile schema is v7, settings schema is v5, plugin API remains 1.0, and Script API remains 1.0.
- The Outputs page now filters by enabled profile outputs and provides collapsible Xbox, keyboard, and mouse monitors.

- Advanced the development product version to `0.25.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.
- Version 1.0, external Plugin SDK, branching Node Editor, Scripting promotion, and future major profile-schema work now have explicit architecture-review gates.

- Advanced the development product version to `0.24.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.

- Advanced the development product version to `0.23.0-dev`; profile schema remains v6, settings schema remains v3, plugin API remains 1.0, and Script API remains 1.0.
- Render object-backed ComboBox labels explicitly throughout Mapping Editor, Profile Library, Signal Flow Inspector, Node Editor, Transform Editor, First Run Setup, Performance Profiler, and Test Runner so selected values show friendly names instead of CLR type names.
- Advanced the development product version to `0.22.0-dev` and application settings to schema v3; profile schema remains v6, plugin API remains 1.0, and Script API remains 1.0.
- Replaced the blocking startup ViGEmBus prompt and duplicated detector with one explicit, shared driver service consumed by output and First Run Setup.

- Advanced the development product version to `0.21.0-dev` and profile schema to v6; settings schema remains v2, plugin API remains 1.0, and Script API remains 1.0.

- Advanced the development product version to `0.20.0-dev`; profile schema remains v5, settings schema remains v2, plugin API remains 1.0, and Script API is 1.0.

- Advanced the development product version to `0.19.0-dev`; profile schema is v5 while settings schema remains v2 and plugin API remains 1.0.
- Advanced the development product version to `0.17.0-dev` for release-preparation validation.
- Advanced the development product version to `0.18.0-dev` while retaining profile schema v4, settings schema v2, and plugin API 1.0.
- Promoted `node-editor` from a reserved Hidden flag to an enabled Beta composition boundary.
- Added coverage collection to both automated test projects and merged reporting by source line.
- Startup composition now respects Keyboard Output, Analog PWM, Signal Flow, Plugin SDK, recording/playback, and Debug tooling flags.
- Application settings schema advanced additively to v2; profiles remain schema v4.
- Restored the Mapping Editor's bounded three-column layout to eliminate circular WPF measurement and UI lockups.
- Standardized Recent Raw Input and both Xbox visuals at a compact 450 x 220 footprint with aligned Dashboard content.

### Safety

- Automated validation does not declare release readiness while required hardware, installer, migration, runtime-policy, or soak evidence is incomplete.
- Release builds continue to exclude Debug dashboard, profiler, and Test Runner code.
- Composition changes require restart and never delete disabled-feature configuration.

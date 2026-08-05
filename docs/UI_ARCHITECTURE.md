# UI Architecture

## Scope

HOTASBridge uses a WPF MVVM shell over the runtime, profile, mapping, transform, input, and output services. The UI never reads hardware or writes output devices directly.

```text
WPF Views
  -> MainViewModel and focused page view models
  -> Core service abstractions
  -> Input / Mapping / Output / Diagnostics services

Runtime Signal Cache + Telemetry
  -> throttled view-model updates
  -> Device Inspector / Signal Flow / Output Monitor / workspace panes
```

## MVVM Responsibilities

| Layer | Responsibility |
| --- | --- |
| Views | Layout, templates, bindings, WPF-only input events, and accessibility metadata. |
| View models | Selection, commands, filtering, navigation, notification history, and presentation state. |
| Core | Profiles, mappings, RuntimeSignals, workspace configuration contracts, validation, and service interfaces. |
| Infrastructure | Atomic JSON persistence for profiles, settings, presets, and workspace layouts. |
| Runtime services | Hardware discovery/input, serialized mapping/output session lifecycle, signal processing, output actions, scheduling, and diagnostics. |

The small code-behind files are limited to WPF initialization, selection synchronization, double-click handling, keyboard capture hooks, and shell geometry capture/restore.

`MainViewModel` is the application shell coordinator. It owns application startup/shutdown, Dispatcher sampling, application-settings persistence, and explicit reactions that span independent page/runtime owners. Profile persistence and CRUD, device discovery and membership, input monitoring, runtime mapping/session behavior, mapping authoring, navigation, page collections, activity feeds, notifications, and feature-flag presentation live in focused coordinators or view models. `DashboardView`, `DevicesView`, `DeviceInspectorView`, `MappingEditorView`, `AxisCurvesView`, `SettingsView`, `AboutView`, and `DiagnosticsView` now own their presentation while inheriting the shell data context. Focused pages bind directly to their presentation owners where state is not intentionally shared. Command, persisted application settings, and cross-page state remain in their existing owners. TD-010 is complete: `MainWindow` now contains only shell composition, global commands, workspace regions, and the status bar.

## Shell Layout

The application shell contains:

1. Persistent primary navigation.
2. Page title, current notification, workspace selector, and global commands.
3. A center document region containing the existing page tree.
4. Optional left, right, and bottom tool-pane regions.
5. An always-visible status bar.

The center page region remains stable while workspace panes move around it. This preserves every completed page and avoids coupling page navigation to docking state.

## Status And Notifications

`NotificationCenterViewModel` owns the current shell status and the observable Recent Activity collection. Status updates made during startup remain visible without filling history; history is enabled only after initialization. Later distinct messages are classified using the existing information/warning/error word rules, inserted newest first, and capped at 100 entries. `MainViewModel` keeps a compatibility status setter and collection proxy so page callbacks, scripts, macros, the status bar, and workspace bindings remain unchanged.

This collection is presentation history only. Structured logs, runtime diagnostics, and telemetry remain the durable diagnostic evidence.

## Workspace Model

Workspace layouts store UI state only:

- Pane ID
- Dock region
- Display order
- Visible state
- Pinned state
- Left/right/bottom region sizes
- Main-window size, screen position, and maximized state
- Devices-grid column widths and display order

The built-in layouts are Flight Setup, Mapping Mode, Diagnostics, and Development. Users can save and delete named custom layouts or restore Flight Setup.

Workspace configuration is written atomically to:

```text
%LOCALAPPDATA%\HOTASBridge\Workspaces\workspace-layouts.json
```

It is independent from profile JSON. Switching, exporting, or migrating a profile cannot modify the workspace.

Window and Devices-grid state are captured during coordinated shutdown and restored before the main window is shown. `DevicesView` owns its grid-column capture/restore details while the shell coordinates workspace persistence. Invalid off-screen bounds fall back to centered startup, and damaged dimensions are clamped during workspace normalization.

## Runtime Data

Workspace panes consume existing view-model state:

- Runtime: active profile, mapping state, device count, rates, and scheduler latency.
- Profile Health: shared profile validation report.
- Recent Activity: bounded UI notification history.
- Outputs: Output Manager diagnostics.
- Diagnostics: existing diagnostic event collection.

No pane introduces a hardware access path.

## Themes

Light, Dark, and System modes use application-level dynamic brush resources. System mode reads the Windows application theme. Theme choice is stored in application settings, not profiles.

## Performance

- Runtime signals are coalesced by control and sampled on the WPF render priority at approximately 60 FPS.
- Output Monitor consumes Output Manager diagnostics at approximately 30 FPS so Xbox state remains smooth without monopolizing the dispatcher.
- Signal Flow refreshes at approximately 30 FPS only while its page is visible because rebuilding stage collections is comparatively expensive.
- Device Inspector updates live control state on every sampled change, but records event history only while the page is visible; stable hat-cell, encoder, and switch view models update properties in place rather than rebuilding item templates.
- Analog event history is limited to 20 entries/sec per control and filtered collections are updated incrementally rather than cleared and rebuilt.
- `RuntimeActivityViewModel` owns the Dashboard recent-event and input-diagnostics collections; meaningful analog activity is sampled to 10 Hz globally, Recent Events retain 150 rows, and connection diagnostics retain 200 rows.
- Workspace panes bind to throttled or bounded collections, and changing a workspace does not rebuild runtime services.

## Easy and Advanced Presentation

`NavigationViewModel` owns the visible destination collection, selected item, null-safe page key, Easy/Advanced rebuilds, and the command shared by the sidebar and quick actions. The tested Core `InterfacePresentationPolicy` defines Easy and Advanced destination catalogs plus feature requirements; the App filters those requirements through `IFeatureFlagService`. `MainViewModel` retains only page-entry effects such as refreshing diagnostics. Easy Mode shows Home, Input Devices, Test Inputs, Mappings, PWM, Macros, Outputs, Profiles, Settings, and About. Both modes resolve the same `HotasProfile`, mappings, Runtime Signal Cache, and runtime services. About remains common so setup recovery and offline third-party notices are always reachable.

Fresh and upgraded installations lock the presentation to Easy Mode. General Settings provides the deliberate owner action that unlocks Advanced Mode and exposes the mode selector, advanced settings, diagnostics, and editor controls. Locking it again immediately rebuilds Easy navigation. Switching or locking modes never edits a profile. Easy Mode displays a notice only when a profile contains custom transforms, conditions, macros, or non-default layers that its simplified editor cannot edit. Those mappings remain active and persisted.

### Feature Flags

`SettingsView` owns the General, Devices, Profiles, Output, Diagnostics, Developer, and Advanced tab presentation. Application choices and persistence remain shell responsibilities. The Developer tab binds build text, enabled summary, pending-restart state, and feature rows directly to `FeatureFlagSettingsViewModel`; `MainViewModel` retains override persistence into application settings, Project Health refresh, and restart notifications. Runtime composition and output availability continue to use the immutable startup snapshot until the application restarts.

### Dashboard

`DashboardView` owns the Home/Dashboard presentation. It preserves two fixed 450x220 live panels for recent raw input and generated Xbox state, reads its bounded activity feed directly from `RuntimeActivityViewModel`, and uses shell commands/state only for application-wide profile, navigation, setup, and mapping operations.

### Devices

`DevicesView` binds its search, physical/virtual/status filter, input-source selection, demo visibility, visible-count text, and rows directly to `DeviceBrowserViewModel`. The view also owns Devices-grid column layout capture/restore. `MainViewModel` coordinates refresh requests and keeps Add, Enable, Disable, and Remove commands because those operations cross profile membership, inspector, monitoring, and runtime boundaries. Discovery, provider correlation, stable identity reconciliation, profile mutation, and selected-device monitoring remain behind `IDeviceCoordinator` and `IInputMonitoringCoordinator`.

`DeviceInspectorView` owns the complete tabbed diagnostics presentation and binds its items directly to `ProfileDeviceTabsViewModel.Tabs`. The focused tab owner rebuilds inspectors from enabled devices in the active profile that match the current detected-device list. The selected device intentionally remains shared through mapping-selection state because Mapping Editor, Learn Mode, curves, macros, and signal-flow diagnostics coordinate on the same device. The shell supplies the inspector factory and retains selection restoration, input-monitoring restart, telemetry, scripting, and other cross-page refresh effects.

### Guided Setup

The optional first-run workflow supports back/next navigation through device detection, driver verification, multi-device selection, input testing, output-type selection, profile selection, starter mapping, output verification, and finish. It returns selected device IDs and output plugin IDs to the existing profile model. Upgraded installations skip automatic setup; Help/Settings can reopen it.

### Profiles

`ProfileCommandsViewModel` owns New, Save, Import, Export, Duplicate, Save As, Rename, Delete, and template-create command instances plus their operation-name/template selection state. `IProfileDialogService` owns WPF file pickers and destructive-delete confirmation. The tested Core `IProfileManagementCoordinator` owns persistence sequencing, accepted-state updates, and transactional last-profile replacement/rollback. `ProfileCollectionsViewModel` owns the observable profile and recent-profile collections, collection mutations, recent deduplication/order/retention, and presentation synchronization. `MainViewModel` retains recent-profile settings persistence, active-profile runtime reactions, validation refresh, profile-library view refresh, and compatibility proxies for unchanged XAML.

`ProfilesView` keeps automatic process activation separate from the visible running-application picker. Background activation observes process snapshots without changing UI collections; the picker uses an explicit, virtualized snapshot refresh. Legacy Difficulty and Aircraft metadata remain schema-compatible but are no longer presented because Category and Vehicle cover the active workflow.

### Mapping Editor

`MappingEditorView` owns the existing three-column Input -> Processing -> Output presentation. It binds device tabs, live controls, saved rows, Easy presets, and Learn Mode directly to `ProfileDeviceTabsViewModel`, `MappingCollectionsViewModel`, `MappingEditorSelectionViewModel`, and `InputLearnViewModel`. The editor deliberately avoids an outer horizontal `ScrollViewer`: its cards derive finite width from the control list, preventing the infinite-measure cycle that previously caused UI-thread layout churn. `MappingEditorSelectionViewModel` owns selected device, control, output mode, Xbox/mouse target, hat direction, pointer settings, easy preset, mapping behavior, keyboard shortcuts, visual modifiers, selected-key lookup, assigned/live keyboard overlays, Xbox/mouse/visual-key target commands, and physical keyboard capture/clear commands. `MappingCommandsViewModel` owns demo-mapping, Easy preset, create/update, cancel-edit, and delete-selection command instances plus enablement refresh. `MappingAuthoringViewModel` owns create/update request snapshots, output and Analog PWM validation, Easy presets, demo and learned mapping creation, edit loading/cancellation, and selected-mapping deletion. `MappingCollectionsViewModel` owns saved rows, selected-device control options, duplicate-device warnings, and deterministic rebuild rules. `MainViewModel` retains selected-control clearing, runtime transitions, navigation, cross-page refresh, and status callbacks. `IKeyboardCaptureDialogService` owns WPF dialog creation, owner assignment, and capture-session composition; the view model consumes only its nullable result. Mapping mutation runs through the tested Core `MappingProfileEditor`. Easy Mode and Advanced Mode use the same model, and output selection retains Xbox, keyboard, mouse, and guided Analog PWM authoring.

`InputLearnViewModel` owns Learn Mode session/candidate state, start/cancel/retry/confirm commands, selected-device or device-group scope text, signal detection, and control highlighting. `MappingEditorView` binds to that owner directly. It consumes the Runtime Signal Cache snapshot for its noise baseline and receives only the coalesced UI signal stream while listening. `MainViewModel` retains only the narrow confirmation callback that coordinates profile mapping creation and cross-page refresh.

### Axis Curves

`AxisCurvesView` owns the three-column visual editor and binds targets, selection, editable processing settings, curve type, live raw/processed values, title, Save, and Reset directly to `AxisCurveEditorViewModel`. `CurveEditorControl` receives the same editable deadzone, inversion, exponent, sensitivity, and live position values, so its preview line follows the current unsaved controls. `AxisCurveProfileEditor` keeps profile mutation, matching-mapping synchronization, saved/mapping/default lookup, range defaults, summaries, and deep-copy rules in Core with focused tests. Runtime values continue to arrive through the coalesced UI signal sampler rather than direct hardware access.

### Macro Editor

The Advanced/Beta Macro Editor owns presentation state only. It edits the active profile's existing macro and runtime-variable objects, uses Core validation and edit helpers, and asks the application coordinator to refresh runtime configuration. A 350 ms debounce prevents each WPF keystroke from rebuilding the active macro runtime.

New definitions are disabled by default. Delete remains confirmed and non-destructive until the profile is saved. Friendly option records keep stable device/control and target IDs out of display text without weakening persistence identity.

### Output Monitor

The dedicated Outputs page, not the dashboard, owns full generated-state visuals. Profile-enabled Xbox, keyboard, and mouse panels are filtered through `OutputProfileUsage` and can collapse independently. All state comes from `IOutputManager` diagnostics.

### Head Tracking

`HeadTrackingViewModel` owns profile-backed provider/output selection, activation learning, Hold/Toggle/pass-through, recenter/loss policy, tuning, status text, and throttled runtime snapshot presentation. It depends only on the provider catalog, head-tracking runtime, application action engine, active profile/signal delegates, save queue, and UI dispatcher.

`HeadTrackingView` presents Source, Activation, Live Pose, and selectable Absolute Position, Relative Movement, and Velocity tuning. Maximum velocity is shown only for Velocity mode. The view never reads UDP/shared memory, physical hardware, or the Mouse Output plugin. Learn mode accepts only selected-profile button/switch RuntimeSignals already delivered by the input layer.

The page is available in Easy and Advanced catalogs because configuration does not require exposing Advanced mapping internals. Planned providers remain visibly unavailable rather than silently falling back to another source.

# Navigation

## Primary Structure

| Order | Page | Navigation key |
| --- | --- | --- |
| 1 | Dashboard | `Dashboard` |
| 2 | Devices | `ConnectedDevices` |
| 3 | Device Inspector | `DeviceInspector` |
| 4 | Mapping Explorer | `MappingExplorer` |
| 5 | Mapping Editor | `Mappings` |
| 6 | Node Editor | `NodeEditor` |
| 7 | Profiles | `Profiles` |
| 8 | Output Monitor | `XboxOutput` |
| 9 | Signal Flow Inspector | `SignalFlowInspector` |
| 10 | Macro Editor | `MacroEditor` |
| 11 | Macro Debugger | `Macros` |
| 12 | Axis Curves | `AxisCurves` |
| 13 | Transform Editor | `TransformEditor` |
| 14 | Diagnostics | `Diagnostics` |
| 15 | Project Health | `ProjectHealth` |
| 16 | Settings | `Settings` |
| 17 | About | `About` |

Node Editor, Macro Editor, and Macro Debugger are included while their Beta feature flags are enabled. Developer Dashboard, Performance Profiler, and Test Runner are appended only in Debug builds. Their views and ViewModels are removed from normal Release compilation.

Dashboard is rendered by the focused `DashboardView` and appears as Home in Easy Mode. UI Automation verifies its recent-input and generated-Xbox panels after switching an existing installation to Easy Mode.

Diagnostics is an Advanced Mode destination rendered by the focused `DiagnosticsView`. It reads the bounded `RuntimeActivityViewModel` feed directly, and UI Automation verifies the destination after switching an existing installation from Easy to Advanced Mode.

Devices is rendered by the focused `DevicesView` in both interface modes. It reads browser presentation state directly from `DeviceBrowserViewModel`, preserves workspace-owned grid sizing/order, and is asserted by Easy Mode UI Automation.

Device Inspector is rendered by the focused `DeviceInspectorView` and appears as Test Inputs in Easy Mode. Its tab surface is asserted even when the active profile has no hardware devices.

Easy Mode shows Dashboard, Devices, Device Inspector, Mapping Editor, Output Monitor, Profiles, Settings, and About. About is rendered by the focused `AboutView` and remains available so every user can reopen First Run Setup and read bundled third-party notices without switching to Advanced Mode. UI Automation verifies both actions from Easy Mode.

## Navigation Rules

`NavigationViewModel` owns selection and commands. `InterfacePresentationPolicy` is the single tested catalog for both modes, and `IFeatureFlagService` removes unavailable Advanced destinations before they reach the UI.

- Primary navigation changes the center document only; workspace panes remain available.
- Double-clicking a Mapping Explorer row opens that mapping in Mapping Editor.
- Selecting a Profile Health issue navigates to the affected page and control where possible.
- Dashboard quick actions use the same navigation command as the sidebar.
- Global Refresh, Start/Stop Mapping, and Save Profile remain available in the shell header.

## Page Roles

- Dashboard: runtime overview, profile health, activity, output state, and quick actions.
- Devices: discovery, search, filter, membership, enable/disable, and removal.
- Mapping Explorer: bulk management, sorting, grouping, delete/undo, and editor entry.
- Mapping Editor: focused `MappingEditorView` for visual input-to-output authoring and Learn Mode; its Easy preset selector is exercised by UI smoke.
- Node Editor: graph-based editing of the same mapping model, transform chain, validation, and live flow diagnostics.
- Macro Editor: profile-owned trigger, condition, action, repeat, and runtime-variable authoring with validation.
- Macro Debugger: running/waiting macro state, actions, variables, scheduler timing, errors, and execution controls.
- Axis Curves: focused live curve editor with raw/processed preview, deadzones, inversion, sensitivity, Save, and Reset; its type selector is exercised by Advanced Mode UI smoke.
- Project Health: architecture, documentation, coverage, hardware, issues, debt, feature policy, and release acceptance.
- Settings: focused `SettingsView` with General, Devices, Profiles, Output, Diagnostics, Developer, and Advanced categories; Easy/Advanced mode switching is exercised by UI smoke.

## Future Navigation

Plugin Manager and Script Editor remain future pages. Cloud Sync and the online profile library are shelved. Future pages should be inserted without changing existing navigation keys.

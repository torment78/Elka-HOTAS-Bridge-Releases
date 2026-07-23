# MainViewModel Decomposition Foundation Completion Report

## Scope

This audit closes TD-002 at the architecture-foundation level. It verifies that subsystem behavior and page-specific state have focused owners while preserving `MainViewModel` as the explicit WPF application-shell coordinator.

## Acceptance Audit

| Required owner | Result |
| --- | --- |
| Profile coordinator | `IProfilePersistenceCoordinator`, `IProfileManagementCoordinator`, `ProfileCommandsViewModel`, `ProfileCollectionsViewModel`, and dialog boundary |
| Device coordinator | `IDeviceCoordinator`, `DeviceBrowserViewModel`, and `ProfileDeviceTabsViewModel` |
| Input monitoring coordinator | `IInputMonitoringCoordinator` |
| Runtime coordinator | `IRuntimeMappingCoordinator` and `IRuntimeSessionCoordinator` |
| Page-specific view models | 27 focused view models including Mapping, Curves, Devices, Navigation, Diagnostics, Outputs, Macros, Node Editor, Settings, Profiles, and developer tools |
| Composition root | Microsoft DI through `ApplicationComposition` |
| Regression protection | Core/integration/scripting/architecture tests, Debug/Release builds, and isolated WPF smoke |

## Intentional Shell Responsibilities

- Application initialization and coordinated shutdown.
- WPF Dispatcher ownership and sampled/coalesced UI updates.
- Active-profile reactions that refresh several independent page/runtime owners.
- Composition-facing service references and lifecycle event subscriptions.
- Compatibility properties and commands required by the current monolithic MainWindow XAML.
- User-facing status publication across independent subsystems.

These responsibilities are not moved into callback-only wrappers because the shell is their natural coordination boundary. MainWindow page extraction and removal of compatibility proxies remain TD-010.

## Delivered Ownership Boundaries

- Profile persistence, management transactions, commands, dialogs, collections, recents, validation, and local library workflows.
- Device discovery/membership, Devices presentation synchronization, selected profile tabs, and input-only monitoring.
- Runtime mapping, signal dispatch, output session lifecycle, mapping profile mutation, authoring, selection, Learn Mode, and derived collections.
- Navigation, notifications, Dashboard/runtime activity, Axis Curves, transforms, macros, Node Editor, Signal Flow, Output Monitor, Project Health, developer tools, and feature-flag settings.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed during the closure audit.
- Existing compatibility proxies remain until page views are extracted under TD-010.
- The active-profile fan-out remains explicit and ordered in the application shell.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Existing repeated isolated WPF startup smoke remains green across fresh, existing-install Easy Mode, and Safe Mode policies.

## Remaining Work

TD-010 tracks decomposition of `MainWindow.xaml` into focused views and the eventual removal of temporary shell binding proxies. Release blockers, hardware validation, telemetry history, event-bus expansion, and other open debt remain independent backlog items.
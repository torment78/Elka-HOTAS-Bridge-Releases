# Profile Workflow Ownership Completion Report

## Scope

This milestone continues TD-002 by separating profile CRUD persistence, profile dialog boundaries, command presentation, and command-entry state from `MainViewModel`. The shell still owns observable collection changes, recent-profile settings, active-profile runtime reactions, and cross-page refreshes.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Forced save/create/template/duplicate/rename/export/delete sequencing | `MainViewModel` | Core `IProfileManagementCoordinator` |
| Last-profile empty replacement and failed-delete rollback | `MainViewModel` | Tested Core coordinator |
| Profile/package file pickers | Two `MainViewModel` partials | `IProfileDialogService` |
| Destructive delete confirmation | `MainViewModel` | `IProfileDialogService` |
| Nine profile command instances | `MainViewModel` | `ProfileCommandsViewModel` |
| Operation-name and selected-template presentation state | `MainViewModel` fields | `ProfileCommandsViewModel` |
| Profile collection and active runtime reactions | `MainViewModel` | Preserved |

## Implementation

- Added `IProfileManagementCoordinator`, `ProfileDeletionResult`, and a Core implementation over the existing store and persistence interfaces.
- Centralized accepted-fingerprint updates after duplicate and rename operations.
- Made last-profile deletion save an empty replacement before removing the original and roll that replacement back if original deletion fails.
- Added App-owned import/export picker and delete-confirmation services with the active main window as owner.
- Added `ProfileCommandsViewModel` and preserved all existing MainWindow and ProfilesView bindings through compatibility proxies.
- Reused the dialog service for normal profile export and Profile Library package import/export.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Existing profile validation, migration, backups, recent list, active-profile switching, Auto Save, comparison, and import preview behavior remain intact.
- Deleting the only profile still creates and selects a saved empty profile.
- Import preview and comparison remain WPF-owned dialogs; only file selection moved behind the service.

## Verification

- Focused Profile Management Coordinator tests: 5 passed, 0 failed.
- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. The shell still coordinates observable profile collection changes, active-profile runtime reconfiguration, recent-profile settings, notifications, and mapping refreshes. Import-preview and comparison windows may later receive their own dialog services when those workflows are extracted; online/community profile services remain shelved.
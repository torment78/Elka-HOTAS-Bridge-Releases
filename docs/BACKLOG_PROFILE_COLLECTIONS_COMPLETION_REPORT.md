# Profile Collections Ownership Completion Report

## Scope

This milestone continues TD-002 by moving the profile and recent-profile presentation collections, collection mutation operations, and recent ordering rules out of `MainViewModel`. Profile persistence and active-profile runtime reactions remain unchanged.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Profile presentation collection | Mutable shell collection | `ProfileCollectionsViewModel.Profiles` |
| Recent profile presentation collection | Mutable shell collection | `ProfileCollectionsViewModel.RecentProfiles` |
| Profile add/remove/replace/refresh | Direct mutations in three partials | Focused owner methods |
| Recent deduplication and ordering | Shell helper | `ProfileCollectionsViewModel.TrackRecent` |
| Ten-item recent retention | Shell helper | Focused owner |
| Recent deletion synchronization | Shell delete workflow | Focused owner |
| Persistence and active runtime transitions | Shell/services | Preserved |

## Implementation

- Added `ProfileCollectionsViewModel` with observable profile and recent-profile collections.
- Routed startup loading, New, template creation, Duplicate, Save As, Rename refresh, Delete, local package import/replace, and First Run additions through typed operations.
- Centralized recent-profile metadata snapshots, duplicate removal, newest-first ordering, ten-item retention, deletion, and collection synchronization.
- Replaced `MainViewModel` collection storage with compatibility proxies so Profile Library views and existing MainWindow bindings remain unchanged.
- Kept settings writes, persistence validation, delete transactions, active-profile selection, runtime refresh, and library filtering in their existing owners.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Profile ordering, import replacement, last-profile replacement, recents metadata, recents limit, active-profile selection, and Save/Auto Save behavior remain unchanged.
- Local profile packages remain supported; deferred online/community/cloud services remain excluded.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: passed twice consecutively across fresh, existing-install Easy Mode, and Safe Mode policies.

## Remaining Work

TD-002 remains in progress. Cross-page profile/device reactions and temporary shell compatibility proxies still need focused owners. Direct WPF view-model unit-test infrastructure remains future work; this App collection slice is protected by tested Core profile-management workflows, compilation, the complete automated suite, and repeated isolated UI Automation smoke.
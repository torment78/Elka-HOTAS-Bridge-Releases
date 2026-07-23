# Profile Device Tabs Ownership Completion Report

## Scope

This milestone continues TD-002 by moving the selected profile's Device Inspector tab collection and deterministic rebuild rule out of `MainViewModel`. Cross-page reactions remain in the shell because they coordinate independent page and runtime owners.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Device Inspector tab collection | Mutable shell collection | `ProfileDeviceTabsViewModel.Tabs` |
| Enabled profile-device filtering | Shell rebuild loop | `ProfileDeviceTabsViewModel.Rebuild` |
| Detected-device stable-ID matching | Shell rebuild loop | `ProfileDeviceTabsViewModel.Rebuild` |
| Inspector construction | Inline shell construction | Supplied factory callback |
| Selected-device restoration | Shell reaction | Preserved |
| Cross-page/runtime refresh | Shell orchestration | Preserved |

## Implementation

- Added `ProfileDeviceTabsViewModel` with a typed inspector factory and an observable tab collection.
- Rebuilds materialize the detected-device snapshot, clear stale tabs, preserve active-profile order, include only enabled profile devices, and match stable IDs case-insensitively.
- Replaced the mutable `MainViewModel.DeviceTabs` property with a compatibility proxy.
- Reduced `RebuildProfileViews` to delegate tab derivation before running its existing selection, mapping, curves, transforms, macros, signal-flow, telemetry, scripting, notification, and monitoring effects.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Tab ordering, enabled-device filtering, stable-ID matching, inspector callbacks, selected-device restoration, and input-only monitoring behavior remain unchanged.
- Device discovery and profile membership remain owned by `IDeviceCoordinator`; Runtime Signals still reach inspectors through the shared cache.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: passed twice consecutively across fresh, existing-install Easy Mode, and Safe Mode policies.

## Remaining Work

TD-002 remains in progress. Profile collection and recent-profile presentation reactions plus temporary shell compatibility proxies still need focused owners. Direct WPF view-model unit-test infrastructure remains future work; this deterministic App collection slice is protected by compilation, the complete automated suite, and repeated isolated UI Automation smoke.
# Derived Mapping Collections Ownership Completion Report

## Scope

This milestone continues TD-002 by moving three derived Mapping UI collections and their deterministic rebuild rules out of `MainViewModel`. It also hardens the WPF startup smoke after validation exposed a pre-existing first-run completion race.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Selected-device mapping controls | Shell collection and rebuild loop | `MappingCollectionsViewModel` |
| Saved-mapping presentation rows | Shell collection and rebuild loop | `MappingCollectionsViewModel` |
| Duplicate-device warnings | Shell collection and rebuild loop | `MappingCollectionsViewModel` |
| Selected-control clearing | Shell reaction | Preserved |
| Runtime mapping transitions and page refresh | Shell orchestration | Preserved |
| Fresh-install smoke readiness | Immediate close after Skip | Waits for main navigation readiness |
| Shutdown timeout evidence | No per-step boundary | Structured step events |

## Implementation

- Added `MappingCollectionsViewModel` with observable controls, mappings, and duplicate-warning collections.
- Centralized each collection's clear/repopulate sequence while continuing to use existing `MappingViewModel` rows and the tested Core `DuplicateInputDetector`.
- Replaced MainWindow-facing collection properties with compatibility proxies.
- Kept selected-control clearing and runtime/page side effects in `MainViewModel` for a later orchestration slice.
- Added stable structured events before awaited shutdown steps and after successful output disposal.
- Changed WPF smoke navigation retries to honor `TimeoutSeconds` and made fresh-install smoke wait for Settings after skipping setup before requesting shutdown.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Device selection, mapping rows, duplicate warnings, Mapping Explorer, Node Editor, transforms, runtime transitions, and status behavior remain unchanged.
- Shutdown ordering is unchanged; only diagnostic events were added.
- Smoke hardening changes test timing only, not product startup behavior.

## Validation Investigation

The first smoke attempts intermittently timed out during immediate post-wizard shutdown or inspected navigation before initialization completed. An A/B run against the preceding validated commit `d569dac` reproduced the same shutdown timeout, ruling out the collection extraction. Retained structured logs showed normal startup and, on a successful run, every shutdown step completed in about 50 ms. The smoke now waits for navigation readiness and passed twice consecutively across fresh, existing-install Easy Mode, and Safe Mode policies.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Hardened isolated WPF startup smoke: passed twice consecutively.

## Remaining Work

TD-002 remains in progress. Profile/device collection reactions and mapping orchestration still belong to the shell. Direct WPF view-model unit-test infrastructure remains future work; this slice is protected by existing Core detection/mapping tests, compilation, the complete automated suite, and repeated isolated UI Automation smoke.
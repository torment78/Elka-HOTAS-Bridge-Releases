# Axis Curves Page Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Axis Curves page selection, live values, target rebuilding, reset, and save orchestration out of `MainViewModel`. Profile mutation, default settings, lookup, summary, and deep-copy rules move to a tested Core helper. Existing curve processing, mapping synchronization, profile schema v7, and XAML remain unchanged.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Axis target collection and selection | `MainViewModel` | `AxisCurveEditorViewModel` |
| Live raw/processed curve values | `MainViewModel` | `AxisCurveEditorViewModel` |
| Curve reset and save orchestration | `MainViewModel` | `AxisCurveEditorViewModel` |
| Target rebuilding and selection preservation | `MainViewModel` | `AxisCurveEditorViewModel` |
| Profile curve save and mapping synchronization | WPF helper methods | Core `AxisCurveProfileEditor` |
| Default settings and deep clone | WPF helper methods | Core `AxisCurveProfileEditor` |
| Existing XAML bindings and commands | `MainViewModel` | Preserved through compatibility proxies |

## Implementation

- Added a focused Axis Curves page view model for targets, selected settings, curve type, live values, title, reset, save, and rebuild behavior.
- Added a UI-independent Core profile editor for saving curves, synchronizing matching mappings, resolving saved/mapping/default settings, creating range defaults, deep cloning, and mapping summaries.
- Removed two mutable fields and the curve-page/profile helper implementations from `MainViewModel`.
- Routed coalesced UI signals through the page owner while retaining the existing approximately 60 FPS sampling path.
- Kept mapping creation on the same Core settings policy so trigger and stick range defaults remain identical.

## Compatibility

- No XAML, profile, settings, workspace, transform, plugin, or output schema change.
- No change to linear, exponential, logarithmic, S-curve, custom-control-point, deadzone, sensitivity, inversion, or live visual behavior.
- Save still updates the per-device axis curve and every matching axis mapping, then synchronizes compatibility descriptors.

## Verification

- Focused Axis Curve profile-editor tests: 5 passed, 0 failed.
- Complete automated suite: 309 passed, 0 failed.
- Merged runtime line coverage: 57.50% (`14,545/25,295`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Mapping commands, Learn Mode presentation, shell navigation, notifications, and remaining shell-owned state should continue moving in separately validated slices. Custom curve-point mouse editing remains a separate product enhancement rather than part of this ownership change.

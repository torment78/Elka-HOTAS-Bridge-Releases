# Mapping Command Presentation Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor and Axis Curves command instances out of `MainViewModel`. Existing XAML bindings remain unchanged through compatibility proxies; profile mutation and cross-page reactions stay in their established owners.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Demo-mapping command | `MainViewModel` | `MappingCommandsViewModel` |
| Easy preset command | `MainViewModel` | `MappingCommandsViewModel` |
| Create/update mapping command | `MainViewModel` | `MappingCommandsViewModel` |
| Cancel edit and enablement refresh | `MainViewModel` | `MappingCommandsViewModel` |
| Delete selected mapping and enablement refresh | `MainViewModel` | `MappingCommandsViewModel` |
| Axis Curves Reset and Save commands | `MainViewModel` | `AxisCurveEditorViewModel` |
| Mapping/profile mutation and view refresh | Existing Core editor and shell callbacks | Preserved |

## Implementation

- Added `MappingCommandsViewModel` as the focused owner of five mapping command instances.
- Kept command availability current through explicit editing-state and selection-state refresh methods.
- Added Reset and Save command instances to the existing `AxisCurveEditorViewModel` page owner.
- Removed seven direct command constructions and two forwarding methods from `MainViewModel`.
- Preserved the existing MainWindow bindings through read-only command proxies.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Mapping creation, editing, presets, cancellation, deletion, curve reset/save, status messages, and view rebuilding retain their existing implementations.
- Core mapping mutation continues through the tested `MappingProfileEditor`.
- Cancel remains enabled only during an edit and Delete remains enabled only when a saved-mapping row is selected.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Mapping orchestration and cross-page collection reactions still belong to the shell, and notifications require a focused owner. Direct WPF command-state unit-test infrastructure remains future work; this slice is protected by compilation, the existing Core mapping tests, the full automated suite, and isolated startup smoke.
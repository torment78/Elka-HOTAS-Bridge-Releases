# Mapping Editor Selection Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor selection and presentation-default state out of `MainViewModel`. It intentionally preserves the existing XAML bindings, three-column layout, mapping commands, profile schema v7, and runtime behavior.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Selected device/control state | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Xbox/mouse/output-mode state | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Hat and pointer options | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Easy preset and mapping behavior | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Default selection rules | Private WPF helpers | UI-independent `MappingSelectionPolicy` |
| Existing XAML bindings | MainViewModel properties | Preserved through compatibility proxies |
| Mapping commands and keyboard capture | `MainViewModel` | Deferred to later TD-002 slices |

## Implementation

- Added a focused child ViewModel with independent property-change notifications for selected input type and output mode.
- Moved eleven mutable Mapping Editor fields out of `MainViewModel`.
- Kept stable parent property names so no layout, binding, or control template changed in this milestone.
- Added a Core policy for default Direct, Momentary, Pulse, and Hat-to-D-pad behavior plus D-pad direction selection.
- Forwarded child notifications to existing computed parent properties such as output summary, PWM eligibility, and hat visibility.

## Compatibility

- No profile, settings, workspace, or transform schema change.
- No Mapping Editor XAML or sizing change.
- No mapping creation, Learn Mode, keyboard capture, mouse output, Xbox output, or runtime execution change.

## Verification

- Focused mapping selection and mapping-engine tests: 30 passed.
- Complete automated suite: 304 passed, 0 failed.
- Merged runtime line coverage: 57.52% (`14,437/25,101`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Mapping Editor commands, keyboard presentation state, Learn Mode presentation, Axis Curves presentation, shell navigation, and other page-specific state should continue moving in separately validated slices. TD-010 XAML modularization remains separate so layout changes are not mixed with state ownership changes.

# Mapping Editor Keyboard State Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor keyboard selection and visual-key state out of `MainViewModel`. It preserves the existing visual keyboard, capture dialogs, Analog PWM workflow, mapping schema v7, commands, bindings, and output behavior.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Primary and negative keyboard assignments | `MainViewModel` fields | `MappingEditorSelectionViewModel` |
| Visual modifier selection | `MainViewModel` field and command logic | `MappingEditorSelectionViewModel` |
| Selected visual key lookup | `MainViewModel` | `MappingEditorSelectionViewModel` through existing proxy |
| Assigned-key overlays | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Live held-key overlays | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Mapping configuration parsing | `MainViewModel` helpers | Mapping Editor selection owner |
| Physical keyboard capture dialog | Shell-owned WPF workflow | Preserved for a later command-boundary slice |

## Implementation

- Extended the existing Mapping Editor selection owner with keyboard layout access, selected shortcuts, visual modifiers, assigned-key summaries, and active-key state.
- Moved keyboard mapping load/parsing and visual-key selection behavior out of `MainViewModel`.
- Removed five mutable keyboard presentation fields and two mapping-parsing helpers from `MainViewModel`.
- Preserved all existing parent property and command names as compatibility proxies for unchanged XAML bindings.
- Kept duplicate checking, capture dialog ownership, mapping mutation, and Analog PWM application at their existing orchestration boundaries.

## Compatibility

- No XAML, profile, settings, workspace, plugin, or transform schema change.
- No keyboard injection, capture-hook, mapping execution, PWM scheduling, or output reset change.
- Existing US ANSI, Nordic ISO, Follow Windows, clickable-key, capture-dialog, modifier, conflict, and live-key behavior remains available.

## Verification

- Focused Core tests: 202 passed, 0 failed.
- Focused integration tests: 85 passed, 0 failed.
- Complete automated suite: 304 passed, 0 failed.
- Merged runtime line coverage: 57.52% (`14,437/25,101`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Mapping Editor capture commands and mapping mutation, Learn Mode presentation, Axis Curves presentation, device browsing, shell navigation, and other page-specific state should continue moving in separately validated slices. WPF presentation coverage remains compilation plus startup smoke until dedicated UI instrumentation is introduced.

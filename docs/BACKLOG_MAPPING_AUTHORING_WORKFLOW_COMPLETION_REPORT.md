# Mapping Authoring Workflow Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor creation, update, preset, edit, validation, and deletion orchestration out of `MainViewModel`. It also hardens the existing WPF smoke against stale UI Automation window trees discovered during repeated validation.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Create and update orchestration | Shell methods | `MappingAuthoringViewModel` |
| Easy preset and demo mapping creation | Shell methods | `MappingAuthoringViewModel` |
| Learned mapping creation | Shell helper | `MappingAuthoringViewModel.AddMapping` |
| Edit load, cancel, and action state | Shell field and helpers | `MappingAuthoringViewModel` |
| Output and Analog PWM validation | Shell helpers | `MappingAuthoringViewModel` |
| Selected-mapping deletion | Shell method | `MappingAuthoringViewModel` |
| Profile mutation rules | Core `MappingProfileEditor` | Preserved |
| Cross-page refresh, navigation, and status | Shell callbacks | Preserved |

## Implementation

- Added `MappingAuthoringViewModel` with the active profile, mapping selection, axis settings, Analog PWM settings, selected mapping, feature availability, refresh, navigation, output-notification, and status boundaries supplied as narrow callbacks.
- Moved authoring-request construction, Easy preset application, demo/learned mapping creation, edit loading/cancellation, create/update validation, output summaries, and deletion into the focused workflow owner.
- Forwarded edit-state notifications and command enablement through existing `MainViewModel` compatibility properties so MainWindow XAML remains unchanged.
- Retained all profile mutations in the tested Core `MappingProfileEditor`, including output enablement, mutable configuration cloning, and compatibility synchronization.
- Changed UI Automation navigation and ComboBox lookup to reacquire the current automation root from the native window handle on each retry.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Xbox, keyboard, mouse, hat, pointer, axis-curve, Analog PWM, Easy preset, Learn Mode, Mapping Explorer, and delete/save behavior remain unchanged.
- Runtime transitions, selected-control clearing, profile health, telemetry publication, navigation, and status messages remain shell-coordinated.
- Smoke hardening changes test lookup behavior only; application startup and shutdown behavior are unchanged.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Hardened isolated WPF startup smoke: passed three consecutive runs across fresh, existing-install Easy Mode, and Safe Mode policies.

## Remaining Work

TD-002 remains in progress. Cross-page profile/device collection reactions and compatibility proxies still belong to the shell. Direct WPF view-model unit-test infrastructure remains future work; this App presentation slice is protected by tested Core mapping transactions, compilation, the complete automated suite, and repeated isolated UI Automation smoke.
# Mapping Authoring Boundary Completion Report

## Scope

This milestone continues TD-002 by moving mapping add, update, remove, output configuration, mutable-settings cloning, output enablement, and compatibility synchronization out of `MainViewModel` into a tested Core `MappingProfileEditor`. WPF still owns current selections, feature-policy messages, keyboard capture, duplicate warnings, navigation, and view refresh.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Xbox/keyboard/mouse mapping creation | `MainViewModel` | Core `MappingProfileEditor` |
| Existing mapping retarget/update | `MainViewModel` | Core `MappingProfileEditor` |
| Mapping deletion | `MainViewModel` | Core `MappingProfileEditor` |
| Keyboard and bipolar PWM output configuration | `MainViewModel` | Core `MappingProfileEditor` |
| Required profile-output enablement | `MainViewModel` helper | Core `MappingProfileEditor` |
| Axis/pointer mutable-settings copy | WPF helpers | Core authoring transaction |
| UI validation, status, selection, and refresh | `MainViewModel` | Preserved |

## Implementation

- Added a UI-independent `MappingAuthoringRequest` that snapshots input identity, behavior, selected output, keyboard metadata, optional bipolar PWM, hat options, axis processing, and pointer settings.
- Added Core add, update, remove, output-enable, and pointer-clone operations.
- Made request validation transactional so invalid target/PWM configuration is rejected before an existing mapping is changed.
- Preserved mapping IDs, names, conditions, notes, custom transforms, priorities, and enabled/layer state during edits.
- Centralized Xbox, keyboard, mouse, and PWM cleanup/configuration rules and compatibility-descriptor synchronization.
- Reduced `MainViewModel` by replacing three mapping constructors and output mutation helpers with one request adapter and one refresh method.
- Changed `AnalogPwmEditorViewModel` to provide a detached options snapshot instead of mutating mappings directly.

## Compatibility And Fixes

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, or output schema change.
- Existing mappings and profile schema v7 remain compatible.
- Creating a mapping now consistently enables its required Xbox, keyboard, or mouse profile output.
- Editing a mapping refreshes detached axis settings for the newly selected source control instead of retaining unrelated stale settings.
- Invalid bipolar PWM edits leave the original mapping untouched.

## Verification

- Focused Mapping Profile Editor tests: 9 passed, 0 failed.
- Complete automated suite: 320 passed, 0 failed.
- Merged runtime line coverage: 57.31% (`14,752/25,739`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Mapping command presentation, physical keyboard-capture commands, shell navigation, notifications, and other shell-owned state should continue moving in separately validated slices. Mapping Explorer bulk mutation remains in its existing page owner and refresh callback.

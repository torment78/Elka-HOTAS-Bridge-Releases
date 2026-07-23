# Physical Keyboard Capture Command Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor physical keyboard capture and clear command ownership out of `MainViewModel`. The WPF dialog and low-level hook remain in the App layer, while a narrow service prevents view models from constructing or owning `Window` instances.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Primary shortcut capture/clear commands | `MainViewModel` | `MappingEditorSelectionViewModel` |
| Negative-direction capture/clear commands | `MainViewModel` | `MappingEditorSelectionViewModel` |
| WPF dialog construction and owner assignment | `MainViewModel` | `IKeyboardCaptureDialogService` |
| Physical hook/session construction | Composition argument passed through the shell | App service composed in `ApplicationComposition` |
| Duplicate profile warning policy | `MainViewModel` | Preserved through a typed callback |
| Existing MainWindow command bindings | Shell command properties | Preserved as compatibility proxies |

## Implementation

- Added an App-level `IKeyboardCaptureDialogService` and immutable accepted-result record.
- Kept `KeyboardCaptureDialog` responsible for capture lifecycle, Retry, Clear, Use, generated-input filtering, and warning display.
- Moved all four capture/clear command instances and handlers into `MappingEditorSelectionViewModel`, beside the assignments they mutate.
- Injected duplicate-warning and status callbacks so the child remains independent of profile storage and shell notification implementation.
- Removed the keyboard-capture factory field, dialog construction, and command handlers from `MainViewModel`.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or keyboard-assignment schema changed.
- MainWindow continues binding to the same four public command names.
- Primary/negative duplicate checks, capture cancellation, explicit clear behavior, output-mode selection, and status messages are preserved.
- The low-level hook still ignores HOTASBridge-generated keyboard events.

## Verification

- Complete automated suite: 320 passed, 0 failed.
- Merged runtime line coverage: 57.31% (`14,752/25,739`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Shell navigation, notifications, profile command presentation, mapping orchestration around the Core authoring transaction, and other page-specific state should continue moving in small validated slices. Physical key entry itself still requires manual interaction for hardware-level acceptance because the smoke harness intentionally does not synthesize a key into the low-level capture hook.
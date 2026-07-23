# Backlog Axis Curves View Extraction Completion Report

Date: 2026-07-20

## Scope

Continue TD-010 by extracting Axis Curves and binding its complete editable/live state directly to `AxisCurveEditorViewModel`.

## Existing Implementation Assessment

| Requirement | Before | Result |
| --- | --- | --- |
| Three-column curve editor | Inline shell markup | Extracted unchanged. |
| Targets/settings/live values | Focused owner behind shell proxies | Bound directly; proxies removed. |
| Preview deadzone/curve behavior | `CurveEditorControl` element bindings | Preserved. |
| Hardware-free Advanced validation | Not asserted | Curve selector added to smoke. |

## Implementation

- Added `Views/AxisCurvesView.xaml` and presentation-only code-behind.
- Replaced the inline MainWindow Axis Curves block with `<views:AxisCurvesView />`.
- Bound targets, selected axis, title, settings, curve type, raw input, processed output, Save, and Reset directly to `AxisCurveEditorViewModel`.
- Moved the curve-type option list into the focused editor owner.
- Removed the shell curve aliases, editor subscription, and forwarding method; profile-view selection restoration now addresses the owner directly.
- Preserved all `CurveEditorControl` preview bindings so deadzones and unsaved controls continue to affect the blue processed line.
- Added `Axis curve type` automation identity and Advanced Mode smoke coverage.

## Verification

- Debug build: passed with 0 warnings and 0 errors.
- Isolated WPF smoke opens Axis Curves in Advanced Mode and finds the curve-type ComboBox; fresh, existing, and Safe Mode flows pass.
- Automated tests: 337 passed, 0 failed.
- Merged runtime coverage: 57.55% (`15,059/26,167`).
- Release build: passed with 0 warnings and 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.

## Remaining Work

TD-010 remains active for Settings and legacy compatibility page markup. Continue one protected page at a time.

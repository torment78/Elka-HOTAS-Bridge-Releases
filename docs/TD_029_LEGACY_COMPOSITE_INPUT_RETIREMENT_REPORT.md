# TD-029 Legacy Composite Input Retirement Report

Status: Complete

## Existing Implementation Assessment

| Component | Before TD-029 | Result |
| --- | --- | --- |
| `CompositeInputCatalog` | Unreferenced legacy aggregate | Removed. |
| `CompositeInputEventSource` | Unreferenced legacy aggregate | Removed. |
| `CompositeInputProvider` | Active app-facing manager | Preserved unchanged. |
| `InputProviderAdapter` | Active per-provider signal adapter | Preserved unchanged. |
| `IInputDeviceCatalog` | Active provider-specific compatibility contract | Preserved. |
| `IInputEventSource` | Active provider-specific compatibility contract | Preserved. |

## Audit

Repository-wide source, test, composition, and documentation search found no caller for either retired class. The application composition root already constructs separate Windows HID, Raw Input, and simulation `InputProviderAdapter` instances and supplies them directly to `CompositeInputProvider`.

The provider-specific compatibility contracts remain necessary:

- `WindowsHidDeviceProvider`, `WindowsRawInputDeviceProvider`, and `SimulatedInputService` implement `IInputDeviceCatalog`.
- `WindowsHidInputService` and `SimulatedInputService` implement `IInputEventSource`.
- `InputProviderAdapter` converts compatibility `InputEvent` values into immutable RuntimeSignals.

## Change

- Deleted `src/HOTASBridge.Input/CompositeInputCatalog.cs`.
- Deleted `src/HOTASBridge.Input/CompositeInputEventSource.cs`.
- Removed both files from the current architecture inventory.
- Kept all active discovery, live input, signal publication, deduplication, lifecycle, telemetry, and shutdown behavior unchanged.

## Compatibility

- No profile schema change.
- No settings change.
- No public app-facing input contract change.
- No device identity or mapping behavior change.
- No input/output startup order change.

## Validation

- Debug build: passed with 0 warnings and 0 errors.
- Automated tests: 360 passed, 0 failed, 0 skipped.
- Merged runtime line coverage: 58.62% (`15,992/27,279`).
- Release build: passed with 0 warnings and 0 errors.
- Architecture validator: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: passed for fresh, existing Easy/Advanced, and Safe Mode policies.
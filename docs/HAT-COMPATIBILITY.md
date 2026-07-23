# Hat and D-pad Compatibility

This matrix records provider encoding evidence separately from physical-hardware sign-off. Automated rows exercise the same `HatNormalizer`, RuntimeSignal, Mapping Engine, and output paths used by live devices. Hardware rows remain Pending until a person records the actual report values.

## Automated Compatibility

| Device or source | Provider/encoding | Raw examples | Normalized result | Mapping result | Status |
| --- | --- | --- | --- | --- | --- |
| DirectInput POV contract | DirectInput hundredths | `-1`, `0`, `4500` through `31500`, `0xFFFFFFFF` | Center plus eight directions | Xbox, keyboard, mouse | Pass |
| Generic HID hat | HID 0-7, explicit null 8 | `0` through `8` | Eight directions plus center | Xbox, keyboard, mouse | Pass |
| Generic HID hat | HID 1-8, zero center | `0` through `8` | Center plus eight directions | Xbox, keyboard, mouse | Pass |
| Button-backed D-pad | Individual directions | Up/Right/Down/Left booleans | Cardinal/diagonal plus separate center button | Xbox, keyboard, mouse | Pass |
| Simulated PlayStation D-pad | HID 0-7, null 8 | `0`, `2`, `8` | Up, Right, Centered | Xbox D-pad and mouse | Pass |
| HOTASBridge simulation | Simulated zero-based | `0` through `8` | Center plus eight directions | Runtime/cache/learn/inspector | Pass |

Automated coverage also verifies combined, dedicated, and ignored diagonals; separate center press/release; two devices using the same control ID; one centered transition after release; and mouse movement cancellation on center/reset.

## Physical Hardware Matrix

| Device | Input provider | Raw value | Normalized value | Mapping result | Status |
| --- | --- | --- | --- | --- | --- |
| WinWing Orion 2 stick hats | Windows HID | Not recorded | Not recorded | Not recorded | Pending hardware run |
| WinWing Orion 2 throttle hats | Windows HID | Not recorded | Not recorded | Not recorded | Pending hardware run |
| PlayStation controller D-pad | Windows HID or selected live provider | Not recorded | Not recorded | Not recorded | Pending hardware run |
| Existing virtual Xbox output | ViGEmBus | N/A | Xbox D-pad/button state | Driver-dependent | Pending manual confirmation |
| Physical mouse pointer | Windows SendInput | N/A | Relative movement/buttons/wheels | OS-dependent | Pending manual confirmation |
| Physical keyboard output | Windows SendInput | N/A | Scan code/virtual key/modifiers | OS-dependent | Pending manual confirmation |

No physical row is marked Pass by the automated suite. That is deliberate: simulated provider contracts prevent regressions but do not substitute for hardware evidence.

## Manual Validation Procedure

1. Open Test Inputs before starting mappings and select one device tab.
2. For every hat direction, record Device Inspector provider, raw value, normalized direction, direction count, centered state, and center-button state.
3. Return the hat to center and verify one centered change with no repeated idle flicker.
4. Test diagonals in Dedicated and Combined modes.
5. Map the hat to Xbox D-pad, keyboard, mouse pointer, and one mouse button.
6. Confirm pointer movement stops immediately on center and Emergency Release.
7. Disconnect/reconnect the device and repeat one direction plus center.
8. Record firmware, Windows version, provider, result, and notes in this matrix.

## Provider Rules

- Provider metadata and HID logical/null ranges determine decoding.
- `-1` and unsigned `0xFFFFFFFF` are DirectInput center only in the DirectInput contract.
- Center idle and a physical center-button press are separate states.
- Likely duplicate API representations are correlated by strong interface identity and prefer live/control-enumerating providers.
- Processed virtual devices with distinct paths remain selectable.
- Advanced users may include correlated duplicate representations explicitly; exact duplicate stable IDs remain one identity.

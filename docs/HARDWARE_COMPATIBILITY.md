# Hardware Compatibility

## Status Definitions

| Status | Meaning |
| --- | --- |
| Validated | Complete manual checklist passed on recorded firmware/driver/Windows versions |
| User-validated partial | User confirmed core discovery/input behavior; formal checklist incomplete |
| Supported foundation | Uses a compatible generic API path but the specific model is not formally tested |
| Driver-dependent | Requires a separately installed Windows driver |
| Simulated | Covered without physical hardware |
| Not formally validated | Expected to use generic HID but no validation evidence exists |

## Current Matrix

| Device or backend | Firmware | Driver | Windows | Status | Notes |
| --- | --- | --- | --- | --- | --- |
| Generic Windows HID joystick/gamepad/multi-axis controller | Device-specific | Windows HID | Windows 10/11 | Supported foundation | Shared HID value-cap parsing has automated layout, range, signed conversion, clamping, null, and endpoint coverage; model-specific reports still require hardware validation. |
| WinWing Orion joystick/throttle | Not recorded | Vendor/Windows HID | Windows 11 user report | User-validated partial | Real controls were reported visible and working after HID normalization and identity fixes. Formal range/reconnect matrix is still required. |
| vJoy input device | Device-specific | vJoy | Windows 10/11 | Supported foundation | Classified as virtual when path/name evidence identifies vJoy. HOTASBridge does not create vJoy output in Version 2. |
| ViGEm Xbox 360 output | N/A | ViGEmBus 1.22.0 | Windows 10/11 | Driver-dependent | Virtual controller connects while mapping runs. Signed installer is bundled; native failure paths have hardware-free counter/containment coverage, while formal joy.cpl/game acceptance remains pending. |
| HOTASBridge Generic HOTAS simulation | Built in | None | Any supported Windows | Simulated | Automated catalog and live-input coverage. |
| Twin Engine Aircraft simulation | Built in | None | Any supported Windows | Simulated | Scripted and generated input available. |
| Helicopter simulation | Built in | None | Any supported Windows | Simulated | Scripted and generated input available. |
| Racing Wheel simulation | Built in | None | Any supported Windows | Simulated | Scripted and generated input available. |
| Gamepad simulation | Built in | None | Any supported Windows | Simulated | Scripted sequence covered by Integration tests. |
| Thrustmaster HOTAS products | Not recorded | Not recorded | Not recorded | Not formally validated | Generic HID support may apply. |
| Virpil products | Not recorded | Not recorded | Not recorded | Not formally validated | Generic HID support may apply. |
| VKB products | Not recorded | Not recorded | Not recorded | Not formally validated | Generic HID support may apply. |
| Logitech flight/racing products | Not recorded | Not recorded | Not recorded | Not formally validated | Generic HID support may apply. |
| Turtle Beach flight products | Not recorded | Not recorded | Not recorded | Not formally validated | Generic HID support may apply. |

## Validation Checklist

Record evidence for each physical model:

1. Exact product name and USB VID/PID.
2. Device firmware and vendor software version.
3. Driver version and Windows build.
4. Discovery with physical/virtual filters.
5. Stable identity across unplug/reconnect and application restart.
6. Every axis raw range, center, normalized endpoints, and noise behavior.
7. Every button, hat, encoder, and maintained/momentary switch.
8. Poll rate, event rate, missed reports, and processing latency.
9. Profile save/reload and reconnect matching.
10. Representative Xbox and keyboard outputs.
11. Suspend/resume, device removal while mapping, and automatic recovery.
12. Emergency Reset and clean shutdown with no held output.

## Adding Evidence

Do not mark a model Validated based only on discovery. Add firmware, driver, Windows version, test date, checklist result, and any unsupported controls or workarounds. Keep user reports labeled separately from repeatable maintainer validation.

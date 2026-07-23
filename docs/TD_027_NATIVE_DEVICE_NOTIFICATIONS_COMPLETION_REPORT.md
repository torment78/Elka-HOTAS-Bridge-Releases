# TD-027 Native Windows Device Notifications Completion Report

Status: Complete.

## Comparison

| Requirement | Before | Result |
| --- | --- | --- |
| Connection-change trigger | Two-second provider discovery poll | Windows HID arrival, removal, and topology messages wake discovery after a 150 ms coalescing delay. |
| Windows API ownership | No native notification boundary | Input owns `WindowsInputDeviceNotificationSource` and its private message-only Win32 window. |
| Discovery behavior | Periodic enumeration | Native notifications reuse the same serialized provider enumeration, correlation, health, and lifecycle comparison. |
| Missed-message recovery | Next two-second poll | Native mode retains a 30-second safety poll. |
| Registration failure | Not applicable | Startup remains healthy and automatically restores the prior two-second polling cadence. |
| Diagnostics | Device/provider counts and lifecycle events | Availability, received notifications, notification refreshes, fallback refreshes, status, warnings, and logs are exposed. |
| UI coupling | Shell reacts to provider lifecycle events | Unchanged; WPF does not own a native handle or call device APIs. |

## Implementation

- Added the UI-independent `IInputDeviceNotificationSource` contract in Core.
- Added a Windows implementation using a background message-only window and HID `RegisterDeviceNotification`.
- Kept the native callback lightweight: it publishes a typed topology hint and never enumerates, maps, or generates output.
- Added a bounded binary wake signal and 150 ms burst coalescing in `CompositeInputProvider`.
- Preserved its existing enumeration semaphore, correlation rules, lifecycle snapshots, health, telemetry, and event contracts.
- Added 30-second safety enumeration while native notifications are healthy.
- Added automatic two-second polling when no notification source exists or startup fails.
- Stopped and disposed the notification window before provider shutdown.

## Verification

- Focused Input Provider integration tests: 8 passed.
- Full automated suite: 358 passed, 0 failed, 0 skipped.
- Merged runtime coverage: 58.44% (`15,925/27,248`).
- Debug and Release builds: zero warnings and zero errors.
- Architecture validator: zero errors, warnings, or suggestions; architecture-review gates passed.
- Native registration/start/stop test passed without physical hardware.
- WPF smoke: fresh install, existing Easy/Advanced navigation, and Safe Mode startup/shutdown passed with the composed notification source.
- Release readiness remains false because five existing manual release blockers and eight manual acceptance checks remain open.

## Deferred

- Native Windows sleep/resume lifecycle messages remain separate from HID topology notifications.
- SetupAPI Container ID retrieval was completed subsequently by TD-028.
- DirectInput remains deferred until hardware evidence shows HID is insufficient.
- Network input remains intentionally shelved.

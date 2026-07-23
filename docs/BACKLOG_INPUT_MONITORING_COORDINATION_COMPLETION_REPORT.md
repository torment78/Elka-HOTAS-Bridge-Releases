# Input Monitoring Coordination Completion Report

## Scope

This milestone continues TD-002 by extracting selected-device input monitoring and watchdog recovery from `MainViewModel`. It preserves existing device discovery, RuntimeSignal publication, input-only editing, and output start/stop behavior.

## Requirement Review

| Requirement | Before | Result |
| --- | --- | --- |
| Input monitoring lifecycle boundary | ViewModel-owned | Core contract and coordinator implementation added |
| Equivalent selection handling | ViewModel-owned | Coordinator avoids unnecessary provider restarts |
| Device selection changes | Working | Stop-and-restart behavior preserved |
| Watchdog recovery | Direct provider calls | Routed through the same coordinator lifecycle |
| Empty selection | Working | Provider stops and no monitor is started |
| Output isolation | Working | Preserved; monitoring does not start output plugins |

## Implementation

- Added `IInputMonitoringCoordinator` to the Core input contracts.
- Added `InputMonitoringCoordinator` with serialized lifecycle changes, case-insensitive device identity comparison, cancellation ownership, and forced recovery.
- Registered the coordinator in the application DI composition root.
- Replaced `MainViewModel` monitoring state, semaphore, cancellation source, and direct watchdog restart calls with the coordinator boundary.
- Kept discovery and RuntimeSignal event consumption on the existing `IInputProvider` boundary.

## Validation

- Focused coordinator tests: 3 passed.
- Debug build: passed with 0 warnings and 0 errors.
- Full suite: 283 passed, 0 failed, 0 skipped.
- Release build: passed with 0 warnings and 0 errors.
- Architecture Validator: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: passed fresh-install, existing-install/Easy preset, and Safe Mode scenarios.
- Full release automation: passed.
- Release readiness remains false because five known blockers and eight manual hardware/signing checks are intentionally incomplete.

## Remaining Work

- TD-002 remains in progress. Device discovery/selection presentation, runtime command orchestration, and page-specific state still reside in `MainViewModel`.
- Physical hot-plug and real-controller behavior are unchanged and remain covered by the hardware validation checklist.
- This milestone does not add DirectInput or Windows device-notification APIs.

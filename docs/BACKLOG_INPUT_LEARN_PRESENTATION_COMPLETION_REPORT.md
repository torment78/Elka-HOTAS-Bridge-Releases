# Input Learn Presentation Ownership Completion Report

## Scope

This milestone continues TD-002 by moving Mapping Editor Learn Mode listening state, candidate presentation, retry/cancel/confirm commands, signal detection, and control highlighting out of `MainViewModel`. Final mapping creation remains coordinated by the application shell so the existing profile, output-selection, persistence, and runtime behavior stay unchanged.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Learn session and listening state | `MainViewModel` | `InputLearnViewModel` |
| Candidate visibility and scope text | `MainViewModel` | `InputLearnViewModel` |
| Start, cancel, retry, and confirm commands | `MainViewModel` | `InputLearnViewModel` |
| Runtime signal detection and control highlighting | `MainViewModel` | `InputLearnViewModel` |
| Mapping creation and active output selection | `MainViewModel` | Preserved through a narrow confirmation callback |
| Input noise and device-scope policy | Core `InputLearnSession` | Preserved and regression-tested |
| Existing XAML bindings and commands | `MainViewModel` | Preserved through compatibility proxies |

## Implementation

- Added `InputLearnViewModel` as the owner of transient Learn Mode state and presentation commands.
- Routed coalesced runtime signals to the child owner only while it is listening.
- Preserved selected-device scope, whole-profile device-group scope, analog-noise rejection, first-meaningful-input detection, control selection, and inspector highlighting.
- Kept profile mutation in `MainViewModel` behind a typed `InputLearnConfirmationResult` callback.
- Removed the obsolete Learn Mode fields, no-op capture shutdown method, signal handler, highlight helper, and command implementations from `MainViewModel`.
- Added regressions proving invalid signals do not consume a session and stable device identity matching remains case-insensitive.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, or output schema change.
- Learn Mode still listens without activating the virtual Xbox output and still creates the same mapping using the currently selected Xbox target and behavior.
- Stopping mapping still clears transient Learn Mode state and control highlights.

## Verification

- Focused Input Learn Session tests: 6 passed, 0 failed.
- Complete automated suite: 311 passed, 0 failed.
- Merged runtime line coverage: 57.50% (`14,545/25,295`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Remaining mapping commands, shell navigation, notifications, and other shell-owned presentation state should continue moving in separately validated slices. Direct automated WPF command-state coverage remains future infrastructure work; this slice is protected by Core detection tests, WPF compilation, and isolated startup smoke validation.

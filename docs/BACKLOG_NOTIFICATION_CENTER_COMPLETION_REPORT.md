# Notification Center Ownership Completion Report

## Scope

This milestone continues TD-002 by moving current shell status, notification history, severity classification, and retention out of `MainViewModel` into a focused presentation owner. Existing status publishers and WPF bindings remain unchanged through compatibility proxies.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Current status storage | `MainViewModel` field | `NotificationCenterViewModel` |
| Recent Activity collection | `MainViewModel` collection | `NotificationCenterViewModel.Items` |
| Startup history suppression | `MainViewModel` setter condition | Notification center activation lifecycle |
| Severity classification | `MainViewModel.AddNotification` | Notification center |
| Newest-first 100-entry retention | `MainViewModel.AddNotification` | Notification center |
| Cross-subsystem status publication | Shell callbacks | Preserved |

## Implementation

- Added `NotificationCenterViewModel` with a `Ready.` default status, explicit history activation, distinct-status publication, severity classification, and bounded observable history.
- Enabled history immediately after normal application initialization completes, preserving the prior quiet-startup behavior.
- Forwarded status `PropertyChanged` notifications through the existing `MainViewModel.StatusMessage` binding.
- Replaced the shell-owned notification collection with a compatibility proxy to the focused owner.
- Removed the mutable status field and `AddNotification` implementation from `MainViewModel`.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Page view models, runtime callbacks, scripts, macros, and reliability/deployment partials still publish through the same shell status property.
- Repeated identical status text remains suppressed, startup messages remain out of Recent Activity, and later messages retain the same severity words and 100-entry limit.
- Structured logs and telemetry remain independent diagnostic records.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: fresh install, existing-install Easy Mode, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Cross-page observable collection reactions and mapping orchestration still belong to the shell. Direct WPF notification-center unit-test infrastructure remains future work; this slice is protected by compilation, the complete automated suite, and isolated startup smoke.
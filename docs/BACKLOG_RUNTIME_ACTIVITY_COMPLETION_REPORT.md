# Runtime Activity Feed Ownership Completion Report

## Scope

This milestone continues TD-002 by moving the Dashboard Recent Events and input Diagnostics presentation collections, signal throttling, message formatting, and retention out of `MainViewModel`.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Dashboard Recent Events storage | Mutable shell collection | `RuntimeActivityViewModel.RecentEvents` |
| Diagnostics feed storage | Mutable shell collection | `RuntimeActivityViewModel.Diagnostics` |
| Meaningful signal filtering | Shell sampling loop | `RuntimeActivityViewModel.AcceptSignal` |
| Analog activity throttle | Shell timestamp field | Focused owner |
| Recent Events 150-row retention | Shell loop | Focused owner |
| Connection diagnostics 200-row retention | Shell loop | Focused owner |
| Event dispatch and recovery | Shell orchestration | Preserved |

## Implementation

- Added `RuntimeActivityViewModel` with bounded Recent Events and Diagnostics collections.
- Moved current/previous value comparison, the global 100 ms analog throttle, normalized value formatting, newest-first insertion, and 150-row retention into `AcceptSignal`.
- Added explicit append/prepend diagnostic operations so startup lines, Safe Mode priority, bounded connection events, and unbounded provider-error insertion preserve existing behavior.
- Replaced MainWindow-facing shell collections with compatibility proxies.
- Kept Dispatcher ownership, device recovery, discovery refresh, Learn Mode dispatch, telemetry, and output refresh in `MainViewModel`.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, output, or package schema changed.
- Dashboard ordering, value formatting, signal sampling, connection/error text, Safe Mode startup text, status messages, and recovery behavior remain unchanged.
- Structured logs and telemetry remain independent diagnostic records.

## Verification

- Complete automated suite: 327 passed, 0 failed.
- Merged runtime line coverage: 57.27% (`14,850/25,929`); App presentation files remain outside the coverage source set.
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Isolated WPF startup smoke: passed twice consecutively across fresh, existing-install Easy Mode, and Safe Mode policies.

## Remaining Work

TD-002 remains in progress. Cross-page active-profile reactions, feature-flag presentation, and temporary shell compatibility proxies still need focused owners. Direct WPF view-model unit-test infrastructure remains future work; this App presentation slice is protected by compilation, the complete automated suite, and repeated isolated UI Automation smoke.
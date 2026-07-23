# Dependency Injection Foundation Completion Report

## Scope

This milestone addresses TD-001 without changing HOTASBridge runtime behavior. It also reconciles TD-036 and TD-037, whose input-only monitoring and Mouse Output implementations were already delivered and covered by the Easy input/output milestone.

## Requirement Review

| Requirement | Before | Result |
| --- | --- | --- |
| Standard dependency-injection container | Missing | Microsoft DI now validates and resolves the application graph |
| Dedicated composition root | Partial | `ApplicationComposition.cs` owns service registration and construction |
| Thin WPF application lifecycle | Partial | `App.xaml.cs` now retains recovery, startup ordering, watchdog, exception, and shutdown orchestration |
| Preserve runtime ownership | Implemented | Existing `MainViewModel.ShutdownAsync` ordering remains unchanged |
| Input monitoring without outputs | Implemented but stale backlog status | Confirmed and marked complete |
| Mouse Output plugin | Implemented but stale backlog status | Confirmed and marked complete |

## Implementation

- Added `Microsoft.Extensions.DependencyInjection` to the WPF application.
- Registered Core contracts, persistence, input, output, scripting, macro, diagnostics, and deployment services in one approved composition root.
- Resolved `MainViewModel` through `ActivatorUtilities` while retaining explicit values for WPF Dispatcher, Safe Mode, diagnostics paths, and per-use factories.
- Replaced callback closures with a small ViewModel accessor so scripting and macro callbacks preserve the existing late-bound lifecycle without creating a second runtime model.
- Kept service instances externally owned by the established coordinated shutdown sequence; disposing the provider does not replace or reorder runtime cleanup.
- Updated architecture policy to recognize both lifecycle and registration roots.

## Validation

- Debug build: passed with 0 warnings and 0 errors.
- Automated tests: 277 passed.
- Architecture Validator: 0 errors, 0 warnings, 0 suggestions.
- WPF startup smoke: passed fresh-install, existing-install/Easy preset, and Safe Mode scenarios.
- Release build: passed with 0 warnings and 0 errors.

## Remaining Work

- `MainViewModel` remains a broad coordinator and is tracked by TD-002.
- Runtime services continue to use their proven explicit shutdown ownership; moving ownership into container scopes would require dedicated lifecycle regression tests and provides no current user-facing benefit.
- Physical HOTAS and virtual-output acceptance remains manual.

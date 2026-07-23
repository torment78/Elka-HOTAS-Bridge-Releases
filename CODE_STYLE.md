# HOTASBridge Code Style

This guide explains the repository's engineering conventions. `.editorconfig`, `Directory.Build.props`, compiler diagnostics, and architecture validation are the executable source of truth.

## C# Baseline

- Use the repository language version, nullable reference types, and implicit usings.
- Treat warnings as errors and keep builds deterministic.
- Use four spaces in C# and two spaces in XAML, XML, and project files.
- Use file-scoped namespaces where they match the surrounding project.
- Prefer collection expressions and primary constructors only when they improve readability in the existing style.
- Avoid magic values. Name domain thresholds, timing limits, capacities, and protocol constants.

## Naming

- Interfaces use `I` plus a capability name, such as `IProfileManager`.
- Services and managers name the responsibility, such as `OutputManager` or `ProfileService`.
- Runtime models describe runtime intent, such as `RuntimeSignal` and `RuntimeMappingState`.
- WPF view models end in `ViewModel`; views end in `View` or `Dialog`.
- Public members use PascalCase; parameters and locals use camelCase; private fields use `_camelCase`.
- Async methods use the `Async` suffix unless they implement an established interface with a different contract.

## Methods and Types

Keep methods focused and make side effects visible. Extract a helper when it gives a complex operation a useful domain name, removes real duplication, or enables focused testing. Avoid deep nesting, broad coordinator classes, global mutable state, and abstractions with only hypothetical value.

Prefer immutable records for published RuntimeSignals, OutputActions, diagnostics, and other cross-boundary snapshots. Mutable runtime state belongs in an owner service and must not leak into profile serialization.

## Nullability and Validation

Express optional values in the type system. Validate external input, persisted data, plugin data, and Windows API results at subsystem boundaries. Use `ArgumentNullException.ThrowIfNull` for required public arguments where appropriate. Do not silence nullable diagnostics without a documented invariant.

## Async, Threads, and Cancellation

- Do not block the WPF dispatcher with hardware, file, plugin, or long-running work.
- Use the centralized runtime/output scheduler for timers, PWM, repeats, macros, and script callbacks.
- Do not create one thread or long-running loop per mapping.
- Pass cancellation through asynchronous service boundaries.
- Make shutdown deterministic: stop input, drain or cancel work, reset outputs, flush logs, and dispose resources.
- Protect shared runtime data with the narrowest practical synchronization and measure before introducing lock-free complexity.

## Exceptions and Diagnostics

Catch exceptions at subsystem boundaries where they can be classified, logged, isolated, or converted to a safe result. Never silently swallow unexpected failures. Include component, severity, useful state, and exception details without unnecessary personal data.

Runtime components should publish telemetry and diagnostic snapshots through shared services. Avoid UI-only counters and synchronous hot-path file writes.

## Dependency Injection

Major services depend on published interfaces. Construct application services in the composition root or an explicit, documented factory. Direct construction is acceptable for small value objects, deterministic transforms, tests, and locally owned implementation details.

The current application uses manual composition in `App.xaml.cs`; a DI container remains planned technical debt. Do not introduce a second service locator or hidden global container.

## Project Boundaries

- `HOTASBridge.Core`: domain rules, contracts, runtime models, mapping, transforms, and UI-independent services.
- `HOTASBridge.Input`: Windows input providers and hardware-facing adapters.
- `HOTASBridge.Output`: output plugins, Windows output APIs, and driver-facing adapters.
- `HOTASBridge.Infrastructure`: persistence, logging, and configuration adapters.
- `HOTASBridge.ScriptApi`: stable public scripting contracts with no internal dependencies.
- `HOTASBridge.Scripting`: optional language-host implementation over Script API.
- `HOTASBridge.App`: WPF presentation and the composition root.

Core must not reference WPF or implementation projects. Views contain presentation behavior; business logic belongs in Core or a service. WPF-specific code-behind is reserved for focus, input capture, window lifecycle, and control behavior that cannot be expressed cleanly through MVVM.

## Tests

Every behavior change receives a regression test where practical. Prefer deterministic unit tests for transforms and state, integration tests for subsystem flow, simulation for hardware-independent behavior, and documented manual checks for real hardware and WPF interaction.

Tests must not depend on ordering, a developer's profile directory, an installed driver, or network access. Use temporary roots, fixed clocks/seeds, and fakes at published boundaries.

## Documentation

Add XML summaries to public API types and non-obvious public members. Comments explain constraints or intent, not syntax. Architectural decisions that change boundaries, persistence, compatibility, security, or public APIs require an ADR under `docs/adr`.

## Validation

Run:

```powershell
.\scripts\Validate-Architecture.ps1
dotnet build HOTASBridge.sln -c Debug
dotnet test HOTASBridge.sln -c Debug --no-build
dotnet build HOTASBridge.sln -c Release
```

The architecture validator is intentionally conservative and reports some patterns as suggestions. Review suggestions; do not mechanically refactor working code merely to make a heuristic quiet.

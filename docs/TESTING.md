# HOTASBridge Testing Strategy

## Scope

Testing is layered so core behavior can be validated without physical hardware while real-device behavior remains an explicit manual gate.

| Layer | Purpose | Current mechanism |
| --- | --- | --- |
| Unit | Deterministic domain and processing behavior | `HOTASBridge.Core.Tests` |
| Integration | Service boundaries, persistence, simulation, scheduling, and outputs | `HOTASBridge.IntegrationTests` |
| Simulation | Repeatable input without hardware | `SimulatedInputService` and five scenario profiles |
| Regression | Compare known RuntimeSignal sessions and OutputActions | Versioned recordings and `OutputActionRegressionComparer` |
| Hardware | Validate Windows HID, firmware, and driver behavior | Manual compatibility checklist |
| Performance | Detect throughput, allocation, and latency regressions | Debug Test Runner and Performance Profiler |
| UI | Verify WPF navigation and live presentation | Build, UI Automation, and manual visual validation |

The Chapter 14 baseline contains 120 passing automated tests: 77 Core tests and 43 Integration tests.

The current suite contains 342 tests: 227 Core, 98 Integration, 9 Architecture Validator, and 8 Scripting tests. Direct Windows-boundary tests enumerate whatever compatible devices are present, validate model invariants, exercise a no-device HID lifecycle, assess ViGEmBus without launching its installer, and inject a fake Xbox session to verify backend failure counters and cleanup containment. Specific hardware reports and control values remain manual validation.

## Automated Matrix

| Area | Coverage |
| --- | --- |
| Runtime Signals | Immutability, publication, cache, pipeline stages, recording, playback flags, ordering |
| Device identity | Stable matching and reconnect evidence priority |
| Windows HID values | Native value-cap layout, bounded usage enumeration, signed bit-width conversion, clamping, hat null preservation, and exact normalized endpoints |
| Profiles | Serialization, migration, backup, validation, health, deletion persistence |
| Mapping | Lookup, priority, conditions, transitions, runtime state, OutputAction generation |
| Transforms | Calibration, normalization, deadzones, curves, filtering, scaling, inversion, split, threshold, toggle, pulse, PWM, direction |
| Outputs | Xbox state, keyboard state, plugin isolation, reset, scheduler behavior |
| Simulation | Multiple devices, five scenarios, more than 32 buttons, scripted events |
| Recording | Schema round-trip, metadata copy, replay, Mapping Engine integration |
| Scheduler | Queue capacity, drain, rejection, timing, shutdown |
| Reliability | Recovery persistence, watchdog, safe output behavior, crash reports |

## Coverage Measurement

Both automated test projects use `coverlet.collector`. Chapter 17 release validation collects Cobertura reports and merges them by source filename and line number so lines exercised by both projects are counted once.

The measured percentage covers Core, Input, Output, Infrastructure, ScriptApi, and Scripting. WPF presentation code is excluded and uses UI Automation plus visual validation. `Test-FirstRunWizardSmoke.ps1` isolates application data, waits for post-wizard navigation readiness, reacquires the current automation tree from the native window handle before retried navigation and ComboBox assertions, validates fresh/existing/Safe Mode policies, and closes through coordinated shutdown.

Run the complete measurement through:

```powershell
.\scripts\Validate-Release.ps1
```

The generated validation summary records test totals, covered lines, total lines, percentage, scope, build steps, artifacts, and outstanding manual gates.

## Build Validation

Run from the repository root:

```powershell
dotnet build HOTASBridge.sln -c Debug
dotnet test HOTASBridge.sln -c Debug --no-restore
dotnet build HOTASBridge.sln -c Release --no-restore
```

A milestone is complete only when Debug and Release build with zero errors, the full suite passes, required documentation is updated, and hardware-dependent gaps are reported rather than hidden.

## Built-In Test Runner

Debug builds append `Test Runner` to navigation. The page executes:

- compiled Unit and Integration test assemblies;
- hardware/provider discovery checks;
- active-profile validation;
- output plugin and virtual-driver checks;
- runtime and output scheduler diagnostics;
- isolated mapping/transform throughput benchmarks.

Results use Pass, Warning, and Fail. They can be exported to versioned JSON or standalone HTML under `%LOCALAPPDATA%\HOTASBridge\Diagnostics\TestRuns`.

The Test Runner source is removed from normal Release builds by `HOTASBridge.App.csproj`. It does not mutate profiles or hardware configuration.

## Regression Workflow

1. Record a known-good RuntimeSignal session from the Debug Test Runner.
2. Store the recording with the test fixture or validation evidence when appropriate.
3. Replay with timing disabled for deterministic automated tests or enabled for manual timing validation.
4. Capture Mapping Engine OutputActions.
5. Compare action type, mapping, plugin, target, active state, and value tolerance.
6. Treat unexplained differences as regressions.

Recorded input is never confused with live input: replay adds `RuntimeSignalQuality.Replayed` and `RuntimeSignalFlags.Synthetic`.

## Manual Procedures

### New Device

1. Record Windows version, device model, firmware, and vendor driver.
2. Verify discovery, stable identity, reconnect, suspend/resume, every control, normalized ranges, and input rate.
3. Create representative mappings and confirm Xbox or keyboard output.
4. Run Emergency Reset and confirm no output remains held.
5. Add the result to `HARDWARE_COMPATIBILITY.md`.

### New Output Plugin

1. Validate initialize/start/stop/reset/dispose independently.
2. Inject valid and invalid actions.
3. Force plugin failure and confirm other plugins continue.
4. Verify queue, rate, timestamp, error, and health diagnostics.
5. Confirm shutdown releases every output.

### New Transform

1. Cover boundaries, invalid values, inversion, and deterministic repeated execution.
2. Verify per-mapping runtime state is isolated.
3. Validate diagnostics input/output/duration/warnings.
4. Add a replay regression when the transform changes output behavior.

### New Mapping

1. Validate missing device/control/output and unsupported combinations.
2. Exercise press/release or full axis range.
3. Test live update and removal transitions.
4. Save, reload, and verify the mapping remains identical.

### Driver Installation

1. Test a clean machine or VM without ViGEmBus.
2. Verify the signed bundled installer prompt and elevation behavior.
3. Start mapping and confirm the Xbox 360 peripheral appears.
4. Stop mapping and confirm the virtual controller disconnects cleanly.
5. Verify Safe Mode starts without installing or activating output.

### Profile Migration

1. Load each historical schema fixture.
2. Verify an exact backup is created before migration.
3. Compare devices, mappings, transforms, and outputs before and after.
4. Confirm a newer unsupported schema is not overwritten.

## Deferred

CI configuration, a checked-in library of hardware recordings, UI screenshot regression infrastructure, and automated testing on a clean ViGEmBus VM remain future work.

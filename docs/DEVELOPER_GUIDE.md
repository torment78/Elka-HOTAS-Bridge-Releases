# HOTASBridge Developer Guide

## Prerequisites

- Windows 10 or Windows 11 development workstation
- Visual Studio with .NET desktop development, or the matching .NET SDK
- PowerShell 7 for the release-validation script
- ViGEmBus only for manual virtual Xbox output validation

Open `HOTASBridge.sln` in Visual Studio. The `.slnx` file is optional tooling metadata; the solution entry point is the `.sln` file.

## Build

```powershell
dotnet restore HOTASBridge.sln
dotnet build HOTASBridge.sln -c Debug
dotnet build HOTASBridge.sln -c Release
```

Debug includes Developer Dashboard, Performance Profiler, and Test Runner. Release excludes those source files at compile time. Project Health is a stable feature in both configurations.

## Architecture

The primary dependency direction is:

```text
Input Providers -> Runtime Signals -> Mapping/Transforms -> Output Actions -> Output Manager -> Plugins
```

The WPF UI consumes services, caches, telemetry, and immutable snapshots. It does not read hardware directly. Preserve the boundaries documented in [ARCHITECTURE.md](ARCHITECTURE.md).

The Visual Node Editor follows the same boundary. Core projects `InputMapping` into immutable graph records and owns edit history; App renders and coordinates the page. The graph is not a second persistence model. See [NODE_GRAPH.md](NODE_GRAPH.md) and [NODE_EDITOR.md](NODE_EDITOR.md) before extending node types or connection behavior.

Use existing interfaces and adapters before adding a new abstraction. Hardware code belongs in Input or Output implementations; profile configuration and runtime state remain separate.

## Contribution Standards

Repository-wide conventions are defined in [../CONTRIBUTING.md](../CONTRIBUTING.md) and [../CODE_STYLE.md](../CODE_STYLE.md). Consequential boundary, API, persistence, scheduling, security, or dependency decisions are recorded under [adr](adr/README.md).

Run architecture validation before committing:

```powershell
.\scripts\Validate-Architecture.ps1
```

The versioned `architecture.rules.json` policy checks project dependency direction, cycles, WPF isolation, solution membership, public API type documentation, implementation namespace leakage, service construction, and likely service-test gaps. Errors fail validation. Warnings and suggestions require review and, when retained, a technical-debt entry.

See [ARCHITECTURE_VALIDATION.md](ARCHITECTURE_VALIDATION.md) for rule IDs, JSON output, exit thresholds, and CI usage.

Formal milestone reviews use the schedule and records described in [ARCHITECTURE_REVIEWS.md](ARCHITECTURE_REVIEWS.md). Validate the current schedule with:

```powershell
.\scripts\Validate-ArchitectureReviews.ps1
.\scripts\Test-ArchitectureReviewValidation.ps1
```

The Version 1.0, external Plugin SDK, Node Editor expansion, Scripting promotion, and profile-schema gates require review records and linked ADRs before their trigger is accepted.

## Tests

```powershell
dotnet test HOTASBridge.sln -c Debug --no-build
```

The core and integration projects both include the XPlat coverage collector. Chapter 17 coverage merges reports by source file and line so Core lines exercised by both projects are counted once.

WPF behavior is validated with UI Automation, isolated application-data roots, screenshot review, and coordinated-shutdown verification. Hardware-dependent behavior uses the checklist in [HARDWARE_COMPATIBILITY.md](HARDWARE_COMPATIBILITY.md).

## Final Validation

Run the repeatable automated release path with:

```powershell
.\scripts\Validate-Release.ps1
```

This restores, builds Debug, runs architecture and architecture-review validation, runs tests with coverage, builds Release, validates required documentation and JSON schemas, verifies release artifacts, and writes `architecture-validation.json`, `architecture-review-validation.json`, and `validation-summary.json` under `artifacts/validation/<timestamp>`.

Use `-RequireReleaseReady` only for a promotion gate. It returns a non-zero exit code while Project Health contains release blockers, pending required manual checks, incomplete documentation, or partial architecture acceptance.

## Project Health Data

`docs/PROJECT_HEALTH.json` is the machine-readable release-readiness snapshot copied into application output. Update it only from recorded evidence:

- build and test results;
- merged coverage totals;
- hardware checklist records;
- known-issue ownership and target milestone;
- technical-debt register status;
- manual acceptance evidence.

The JSON provider validates schema version, ranges, milestone counts, and unique IDs. Invalid or missing data produces a blocked fallback report rather than preventing application startup.

## Scripting Development

The public scripting contract is isolated in `src/HOTASBridge.ScriptApi`; do not add references from that project to Core, Infrastructure, App, WPF, MoonSharp, or output implementations. Language hosts belong in separate adapter projects such as `src/HOTASBridge.Scripting`.

Lua scripts are discovered from `%LOCALAPPDATA%\HOTASBridge\Scripts` only when the `script-engine` Experimental flag is explicitly enabled. Use an isolated application-data root for automated testing. Script callbacks must use the public host services, execute on the centralized scheduler, remain bounded, and never call hardware or output plugins directly.

Run the dedicated regression project with:

```powershell
dotnet test tests\HOTASBridge.Scripting.Tests\HOTASBridge.Scripting.Tests.csproj -c Debug
```

See `SCRIPT_API.md` before changing a public DTO or interface. API compatibility is independent from the application and profile versions.
## AI Assistant Development

AI providers implement `IAiAssistantService` in Core and consume only immutable `AiAssistantContext` snapshots. Do not pass mutable profiles, hardware/provider instances, output plugins, persistence services, or broad log repositories across this boundary. The initial local provider is deterministic, offline, and exposes no apply operation.

New evidence adapters must identify missing samples explicitly, preserve owning-subsystem warnings/errors, and add focused tests proving that healthy evidence does not produce invented recommendations. Any future remote provider requires the consent and redaction gates in `AI_PRIVACY.md`; any future change workflow requires a separate typed proposal, preview, confirmation, backup, and owning-service apply transaction.

## Feature Flags

Every major incomplete feature belongs in the feature registry with one of these stages: Stable, Beta, Experimental, DebugOnly, or Hidden. Composition-level changes are startup-bound. Do not remove disabled configuration or mutate output services live.

## Profiles And Migrations

Profile schema changes require:

1. a version increment;
2. migration code;
3. an exact pre-migration backup;
4. backward-compatibility tests;
5. schema and migration documentation;
6. retained historical-profile rehearsal before release.

Never serialize runtime output, timer, toggle, or scheduler state into profiles.

New profile formats implement `IProfileImporter` or `IProfileExporter`; they do not enter WPF or `JsonProfileStore`. Preview conversion warnings through `ProfileCompatibilityAnalyzer`, preserve unsupported configuration, and add round-trip, replacement-backup, and comparison tests.

## Change Checklist

Before committing a chapter or behavior change:

1. compare the request against existing behavior and identify any formal architecture-review trigger;
2. add focused regression tests;
3. implement within the owning subsystem;
4. build Debug and Release;
5. run relevant tests and manual validation;
6. update architecture, API, changelog, migration, and health evidence as applicable;
7. leave the working tree buildable and usable.

## Diagnostics And Logging

Use `IStructuredLog`, runtime telemetry, subsystem health, and stage diagnostics. Avoid ad hoc hot-path file writes or UI-only counters. New runtime components should expose current values, timestamps, duration, warning/error state, and a human-readable description.

Crash reports and logs must not include unnecessary personal information. Output plugins must neutralize state during reset, profile change, recovery, and shutdown.

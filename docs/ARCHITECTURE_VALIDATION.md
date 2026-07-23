# Architecture Validation

The HOTASBridge Architecture Validator is a lightweight, UI-independent developer tool. It turns important repository boundaries into repeatable checks without loading application assemblies or requiring hardware.

## Run It

From the repository root:

```powershell
.\scripts\Validate-Architecture.ps1
```

Direct CLI usage:

```powershell
dotnet run --project tools\HOTASBridge.ArchitectureValidator -- `
  --root . `
  --policy architecture.rules.json `
  --format text `
  --fail-on error
```

JSON for CI or diagnostics:

```powershell
.\scripts\Validate-Architecture.ps1 `
  -Format json `
  -OutputPath artifacts\architecture-validation.json
```

`scripts/Validate-Release.ps1` runs the same validator after the Debug build and stores `architecture-validation.json` beside the release-validation summary.

## Policy

`architecture.rules.json` is versioned with the source. It defines:

- the solution and required project membership;
- each project's allowed project references;
- which projects may use WPF;
- public API areas requiring XML type documentation;
- implementation namespace boundaries;
- composition roots and source-analysis scopes.

Update the policy only when an architecture decision changes. Do not add an exception merely to silence a finding. A new project must be added to the solution and policy in the same contribution.

## Findings

| Code | Default impact | Meaning |
| --- | --- | --- |
| `ARCH001` | Error | A project references a project outside its allowed dependency set. |
| `ARCH002` | Error | Project references contain a cycle. |
| `ARCH003` | Error | A non-UI project enables or references WPF. |
| `ARCH004` | Warning/Error | A project is absent from the solution, or the configured solution is missing. |
| `ARCH005` | Warning | A public type in a configured API path lacks an XML summary. |
| `ARCH006` | Suggestion | A public service has no direct textual test reference. |
| `ARCH007` | Suggestion | A service is constructed outside an approved composition root. |
| `ARCH008` | Policy-defined | An implementation namespace leaks outside an approved composition file. |
| `ARCH009` | Suggestion | A discovered project has no explicit policy entry. |
| `ARCH010` | Error | A project file is broken or duplicates another project name. |

Errors fail by default. Warnings identify architecture risk that should be fixed or tracked. Suggestions are review prompts and may include false positives because source scans intentionally avoid a compiler dependency.

## Exit Thresholds

`--fail-on` accepts:

- `error`: fail only for errors; this is the repository and release default.
- `warning`: fail for warnings or errors.
- `suggestion`: fail for any finding.
- `never`: report only; useful for baseline inspection.

The command returns `2` when policy or command input is invalid.

## Current Enforcement

The hard gate protects project direction, cycles, WPF isolation, solution membership, and valid project metadata. Public API documentation and source-level DI/namespace checks are incremental checks so existing production behavior can be improved without a broad rewrite. `App.xaml.cs` owns lifecycle orchestration while `ApplicationComposition.cs` is the approved Microsoft DI registration root for the application service graph.

Known warnings and suggestions belong in `docs/TECHNICAL_DEBT.md`. A finding may be removed by correcting the code, narrowing a demonstrably inaccurate analyzer rule, or documenting an intentional architecture decision through an ADR and policy change.

The current baseline is 0 errors, 0 warnings, and 0 suggestions. Implementation namespace boundaries use Core contracts, service construction is rooted in `App.xaml.cs`, and every public service detected by the current policy has a direct automated test reference. Future findings must be fixed, tested, or recorded as intentional architecture decisions rather than silently excluded.

## CI Integration

A CI job can use:

```powershell
dotnet restore HOTASBridge.sln
dotnet build HOTASBridge.sln -c Debug --no-restore
.\scripts\Validate-Architecture.ps1 -Configuration Debug -NoBuild -Format json `
  -OutputPath artifacts\architecture-validation.json
```

Publish the JSON as a diagnostic artifact even on failure. Keep the default `error` threshold until tracked warnings have been resolved; tightening the gate is an explicit policy decision.

## Scope and Limitations

The validator parses project XML and C# source using deterministic BCL-only analysis. It is not a replacement for compiler diagnostics, Roslyn analyzers, code review, tests, or runtime validation. In particular, test-reference and direct-construction checks are heuristics that require engineering review.

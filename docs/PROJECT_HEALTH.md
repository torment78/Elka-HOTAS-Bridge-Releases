# Project Health

## Purpose

Project Health consolidates release-readiness evidence for developers and project management. It is informational and never changes profiles, mappings, devices, outputs, or feature configuration.

The WPF page consumes the same UI-independent report model used by validation tooling. The report is embedded in both Debug and Release outputs.

## Sources

The tracked snapshot is `docs/PROJECT_HEALTH.json`. It records:

- architecture completion state;
- required-document count;
- measured source-line coverage and scope;
- automated test totals;
- hardware and backend evidence;
- known issues and release blockers;
- active technical debt;
- required manual acceptance checks.

Feature flags are read live from `IFeatureFlagService`, so the page shows the effective state for the current build and settings rather than duplicating them in JSON.

## Status Rules

| State | Meaning |
| --- | --- |
| Complete | Every recorded gate for the area is complete |
| Partial | Evidence exists but the area is not fully accepted |
| Blocked | One or more required release gates are unresolved |
| NotMeasured | No valid measurement has been recorded |

Overall release readiness is Blocked when the report contains a blocker issue, a required incomplete manual check, or a blocked architecture state. It is Partial when no blocker exists but architecture, documentation, coverage measurement, or hardware evidence is incomplete.

## Coverage

Coverage is collected from all test projects and merged by source filename and line number. The same source line is counted once even when both suites exercise it.

Current scope includes:

- HOTASBridge.Core
- HOTASBridge.Input
- HOTASBridge.Output
- HOTASBridge.Infrastructure
- HOTASBridge.ScriptApi
- HOTASBridge.Scripting

WPF presentation code is excluded from this percentage. UI Automation, screenshots, theme checks, and coordinated-shutdown smoke tests provide separate UI evidence.

## Provider Behavior

`JsonProjectHealthReportProvider` validates:

- schema version;
- application/source identifiers;
- milestone and documentation counts;
- coverage ranges;
- unique known-issue and debt IDs;
- required hardware/manual labels.

A missing or invalid report produces a blocked fallback snapshot and a structured diagnostic event. Project Health data never prevents the application from starting.

## Updating Evidence

1. Run `scripts/Validate-Release.ps1`.
2. Record the new test and merged coverage totals.
3. Update hardware evidence only from a completed checklist or explicit user validation.
4. Reconcile known issues and `TECHNICAL_DEBT.md`.
5. Update the report timestamp and application version.
6. Build both configurations and inspect the Project Health page.
7. Commit the report with the code and documentation it describes.

Do not mark a manual check complete because its automated analogue passed. Do not remove an unresolved mapping, driver, runtime, or hardware issue to improve the summary.

## Release Gate

`scripts/Validate-Release.ps1 -RequireReleaseReady` fails when the machine-readable report is not release ready. The ordinary command still produces automated evidence during development while reporting the outstanding manual gates.

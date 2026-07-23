# Architecture Review: Chapter 25 Baseline

- Date: 2026-07-18
- Gate ID: `chapter-25-baseline`
- Scope: Current runtime foundations and retrospective review of Plugin SDK, Node Editor, Scripting Engine, and profile schema v6 boundaries
- Decision owners: HOTASBridge architecture owner
- Reviewers: HOTASBridge engineering review
- Outcome: Accepted with follow-ups
- Related ADRs: [ADR 0001](../adr/0001-preserve-runtime-foundations.md), [ADR 0002](../adr/0002-formal-architecture-review-gates.md)
- Related issues/debt: KI-001 through KI-007; TD-001, TD-002, TD-003, TD-006, TD-032, TD-033, TD-035, TD-040 through TD-044

## Executive Summary

The existing RuntimeSignal, mapping, transform, output, profile, scheduler, diagnostics, plugin-manifest, node-projection, and Script API foundations are accepted as the architecture to evolve. The review does not authorize a runtime rewrite or declare Version 1.0 ready. It adopts prospective gates before public release, external plugin loading, branching graph persistence, scripting promotion, or profile schema v7 and later.

## Evidence

- `HOTASBridge.sln` Debug baseline builds with zero warnings and zero errors.
- Chapter 24 validation recorded 208 passing tests and 55.98 percent merged runtime line coverage (11,809 of 21,094 lines).
- Architecture validation reports 0 errors, 2 tracked boundary warnings, and 10 tracked suggestions.
- Profile migrations support schemas v1 through v6 with backup and automated compatibility coverage.
- Project Health records four pre-existing release blockers, pending physical-hardware and clean-machine evidence, and explicit runtime/deployment risks.
- Feature policy identifies Plugin SDK as Stable foundation, Node Editor as Beta, and Scripting Engine as Experimental and disabled by default.

## Performance

The centralized scheduler, subsystem telemetry, queue metrics, profiler, and throttled UI diagnostics are suitable foundations. Interactive memory and latency have improved, but release-length soak and baseline comparison evidence remains pending under KI-005 and TD-006. No Chapter 25 runtime code is added.

## Maintainability

Project boundaries, immutable signals, output actions, provider/plugin abstractions, architecture validation, and focused test projects provide a workable extension model. MainViewModel coordination, manual composition, implementation namespace leakage, and advisory service-test gaps remain tracked under TD-001, TD-002, and TD-040 through TD-042. These are incremental cleanup items, not grounds for replacing working subsystems.

## Public APIs

Plugin API 1.0 and Script API 1.0 are versioned independently from application and profile versions. The Script API correctly avoids references to Core, WPF, MoonSharp, and implementation assemblies. Public external plugin compatibility is not yet a supported promise and requires its scheduled review before activation.

## Plugin Interfaces

Xbox and keyboard outputs share lifecycle, reset, health, action, and scheduler boundaries. Manifest discovery and compatibility validation form an internal Plugin SDK foundation. External assembly loading, signing, permission, package, trust, and isolation policy remains deferred and is tracked by TD-044 and a prospective review gate.

## Runtime Diagnostics

Runtime Signal Cache, stage diagnostics, telemetry, watchdog health, Output Monitor, Signal Flow, crash reports, performance sessions, and grounded AI Explain evidence provide broad observability. Longer telemetry history/export and backend-specific ViGEm counters remain TD-006 and TD-013.

## Backward Compatibility

Profiles use additive versioned migrations, exact pre-migration backups, validation, and non-destructive missing-device behavior. Node Editor edits the same profile-owned mapping model, and scripting is optional. Historical retained-build migration rehearsal, formal physical hardware reconnect/sleep tests, and signed installer rollback evidence remain release blockers. The current architecture is accepted through profile schema v6 only; a higher schema version triggers a new formal review.

## Findings

| ID | Severity | Finding | Resolution or owner |
| --- | --- | --- | --- |
| F-001 | Decision | Preserve the existing runtime foundations and extend through adapters and published contracts. | ADR 0001 |
| F-002 | Decision | Make milestone architecture reviews executable release and activation gates. | ADR 0002 |
| F-003 | Warning | Version 1.0 lacks formal hardware, installer, migration, runtime-policy, soak, and architecture acceptance evidence. | KI-001 through KI-007 |
| F-004 | Warning | External plugins need trust, compatibility, permission, and isolation policy before support. | TD-044 and `external-plugin-sdk` gate |
| F-005 | Warning | Branching persisted node graphs exceed the reviewed linear projection. | TD-032 and `node-editor-expansion` gate |
| F-006 | Warning | Scripting needs permissions, packages, quotas, and stronger isolation before promotion. | TD-033 and `scripting-engine-promotion` gate |
| F-007 | Warning | Profile migration needs retained-build rehearsal; schema v7 or later requires a new review. | KI-003 and `profile-schema-change` gate |
| F-008 | Advisory | Composition and implementation leakage should be reduced incrementally. | TD-001, TD-002, TD-040 through TD-042 |

## Decision

Accepted with follow-ups. Preserve the current foundations, adopt the living vision and governance policy, and enforce the machine-readable review schedule. Version 1.0 and the prospective expansion gates remain unsatisfied until their own evidence and ADRs are recorded.

## Follow-Up

- Complete the Version 1.0 review only after Project Health release blockers and required manual evidence are resolved.
- Set the corresponding manual gate to `requiredNow: true` before external plugin loading, branching graph work, or scripting promotion begins.
- Keep `reviewedThroughProfileSchemaVersion` synchronized with an accepted review before incrementing profile schema v6.
- Re-run the full review when an accepted foundation decision is superseded.

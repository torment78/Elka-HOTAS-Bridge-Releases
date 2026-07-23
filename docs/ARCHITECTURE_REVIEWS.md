# Architecture Reviews

## Policy

A formal architecture review is a blocking decision gate for changes whose cost or compatibility impact cannot be managed safely through ordinary code review. It evaluates evidence; it is not a meeting substitute or a claim that all future work is complete.

Reviews are required:

- before Version 1.0 release acceptance;
- before supported external Plugin SDK loading or public external-plugin compatibility is enabled;
- before the Node Editor gains a branching persisted graph schema or is promoted beyond its reviewed foundation;
- before the Scripting Engine is promoted beyond its reviewed Experimental boundary or enabled by default;
- before `ProfileMigration.CurrentSchemaVersion` exceeds the version covered by the latest profile-schema review;
- whenever an accepted ADR or governance owner explicitly requires one.

## Required Areas

Every review record contains these sections:

1. Performance
2. Maintainability
3. Public APIs
4. Plugin Interfaces
5. Runtime Diagnostics
6. Backward Compatibility

The review also records scope, evidence, risks, decisions, follow-up owners, linked debt, linked ADRs, and an outcome of `Accepted`, `Accepted with follow-ups`, `Rework required`, or `Rejected`.

## Evidence

Use measured and reproducible evidence where applicable:

- Debug and Release builds and automated tests;
- architecture validator and review-schedule validator reports;
- profiling, queue, memory, CPU, latency, and soak sessions;
- public API and dependency diffs;
- profile migration, backup, rollback, and retained-build rehearsal;
- hardware, driver, installer, recovery, and UI smoke records;
- known issues, technical debt, and feature-policy state.

Unknown or unavailable evidence is recorded as a risk. It is not silently treated as passing.

## Schedule And Enforcement

The versioned schedule is [architecture-review-schedule.json](architecture-reviews/architecture-review-schedule.json). Run:

```powershell
.\scripts\Validate-ArchitectureReviews.ps1
```

Use `-RequireVersionOneReview` when evaluating the Version 1.0 architecture gate directly. `scripts/Validate-Release.ps1` always validates the schedule and includes Version 1.0 review status in release readiness.

The validator checks schedule structure, unique gates, required areas, record and ADR existence, completed-record headings, current profile schema coverage, and gates marked as required now. Its own positive and negative behavior is exercised by `scripts/Test-ArchitectureReviewValidation.ps1`.

## Workflow

1. Open or update the scheduled gate before implementation enters the protected scope.
2. Copy the template and identify decision owners and reviewers.
3. Gather evidence and classify missing evidence explicitly.
4. Record findings and create proposed ADRs for consequential decisions.
5. Select an outcome and assign follow-ups to known issues or technical debt.
6. Accept or reject ADRs, then update the schedule with record and ADR paths.
7. Update the specification, roadmap, Project Health, migration notes, and release notes.
8. Run architecture, review-schedule, test, and release validation.

Changing a gate to `Satisfied` without the required record and ADRs fails validation. Manual gates are changed to `requiredNow: true` as soon as their trigger enters an active milestone.

## Retrospective Adoption

The Plugin SDK foundation, Beta Node Editor, Experimental Scripting Engine, and profile schema v6 existed before this formal policy. The Chapter 25 baseline review records those foundations retrospectively and defines prospective gates for their next compatibility-expanding step. It does not claim the earlier reviews occurred before implementation.

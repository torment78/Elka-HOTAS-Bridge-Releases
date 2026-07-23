# Contributing to HOTASBridge

HOTASBridge is a production-oriented Windows input platform. Contributions must preserve working hardware input, mappings, profiles, Xbox and keyboard output, driver integration, and diagnostics while improving the system incrementally.

Read [CODE_STYLE.md](CODE_STYLE.md), [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md), and [docs/ARCHITECTURE_VALIDATION.md](docs/ARCHITECTURE_VALIDATION.md) before changing a subsystem boundary.

## Engineering Principles

- Prefer clear, focused, testable code over clever abstractions.
- Reuse established interfaces and patterns before introducing a new layer.
- Keep configuration separate from runtime state.
- Keep hardware access in Input and operating-system output in Output.
- Add diagnostics at service boundaries, not as UI-only counters.
- Preserve profile compatibility or provide a versioned migration and backup.
- Do not rewrite a working subsystem solely to make its architecture look cleaner.

## Branches

The current repository uses `master` as its stable/default branch. Treat it as the `main` role described by the long-term branch strategy. Renaming the hosted default branch to `main` requires a coordinated migration and is not part of an ordinary code change.

Recommended branch names:

- `develop`: optional integration branch when parallel release work requires it.
- `feature/<short-name>`: additive functionality.
- `bugfix/<short-name>`: normal defect correction.
- `release/<version>`: release stabilization.
- `hotfix/<short-name>`: urgent supported-release correction.

Keep branches short-lived. Rebase or merge according to the repository host policy, and never rewrite a shared stable branch.

## Development Workflow

1. Compare the request with the current code and documentation.
2. Build the solution and run the relevant baseline tests.
3. Classify requirements as implemented, partial, missing, or deferred.
4. Protect behavior with focused tests.
5. Implement the smallest change in the owning subsystem.
6. Run architecture validation, Debug and Release builds, and relevant tests.
7. Complete manual hardware or UI validation where applicable.
8. Update architecture, API, migration, changelog, and health evidence.

Open `HOTASBridge.sln` in Visual Studio. The `.slnx` file is optional tooling metadata and is not the primary solution entry point.

## Commit Messages

Use an imperative summary that names the outcome:

```text
Add Runtime Signal Cache
Implement Keyboard Output Plugin
Fix mapping deletion persistence
Improve Device Inspector diagnostics
```

Avoid summaries such as `Update`, `Changes`, or `Fix stuff`. Keep unrelated refactoring out of feature and defect commits.

## Pull Requests

A pull request should contain:

- a concise summary and reason for the change;
- the affected architecture boundary;
- test and manual-validation results;
- screenshots for visible WPF changes;
- documentation and migration updates;
- breaking changes, compatibility effects, and rollback notes;
- known limitations or explicitly deferred work.

## Review Checklist

Reviewers verify:

- dependency direction and subsystem ownership;
- readability, naming, and focused methods;
- thread ownership, cancellation, and shutdown cleanup;
- output reset and failure isolation;
- profile compatibility and runtime/configuration separation;
- diagnostics and actionable error handling;
- performance impact on input, mapping, output, and UI paths;
- automated tests and credible manual evidence;
- public API and architecture documentation.

## Dependencies

Before adding a package or native dependency, record its purpose, maintenance status, license, Windows/.NET compatibility, update strategy, and failure behavior. Prefer the platform or existing dependencies when they adequately solve the problem.

## Breaking Changes

A breaking change requires an explicit design review, version impact, migration or compatibility strategy, changelog entry, release notes, and rollback plan. Existing profile data must never be silently discarded.

## Formal Architecture Reviews

Before Version 1.0, supported external plugin loading, branching Node Editor persistence, Scripting Engine promotion, or a profile schema beyond the reviewed version, follow [docs/ARCHITECTURE_REVIEWS.md](docs/ARCHITECTURE_REVIEWS.md). Activate the scheduled gate when work enters scope, record all six review areas, and link accepted ADRs. Run:

```powershell
.\scripts\Validate-ArchitectureReviews.ps1
```

Routine reversible changes continue through normal code review; do not turn every contribution into a formal milestone review.

## Definition of Done

A contribution is complete when:

- code is implemented in the correct project;
- focused tests pass;
- `scripts/Validate-Architecture.ps1` reports no errors;
- `scripts/Validate-ArchitectureReviews.ps1` reports no due or evidence errors;
- Debug and Release builds succeed with no warnings;
- applicable manual validation is recorded;
- documentation and changelog entries match behavior;
- migrations and backups are verified when schemas change;
- no critical warning or unexplained release risk remains.

Run the complete automated gate with:

```powershell
.\scripts\Validate-Release.ps1
```

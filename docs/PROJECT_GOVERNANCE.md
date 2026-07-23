# Project Governance

## Purpose

Governance keeps HOTASBridge extensible without making ordinary work ceremonial. Small reversible changes use the normal contribution workflow. Consequential changes use explicit evidence, decision records, and milestone reviews.

## Sources Of Truth

Use these in order when resolving uncertainty:

1. source code and automated validation;
2. accepted Architecture Decision Records;
3. current architecture, API, schema, and migration documentation;
4. roadmap, Project Health, and technical-debt records;
5. chapter completion reports and historical notes.

Conflicts are corrected in the same change that discovers them.

## Decision Classes

| Class | Examples | Required process |
| --- | --- | --- |
| Routine | narrow bug fix, reversible UI styling, test maintenance | normal review, tests, documentation when behavior changes |
| Architectural | project boundary, scheduler ownership, public API, schema, driver, dependency, security boundary | ADR, architecture validation, compatibility and rollback review |
| Milestone gate | Version 1.0, external Plugin SDK, major Node Editor or Scripting expansion, major profile schema change | formal architecture review record plus linked ADRs before activation or release |

## Ownership

- The owning subsystem defines behavior and diagnostics.
- The composition root wires implementations; it does not absorb business rules.
- Public API owners preserve version negotiation and compatibility notes.
- Profile owners provide migration, exact backups, and retained-format evidence.
- Release owners maintain Project Health and reject unsupported readiness claims.
- Contributors update tests, documentation, debt, and ADRs with the implementation.

One person may hold several roles. The responsibilities still remain distinct in the review record.

## Change Control

Every milestone remains buildable, testable, and usable. Incomplete or risky capabilities remain behind the appropriate feature stage. Existing data is not deleted because a plugin, device, or feature is unavailable.

An architectural proposal must state:

- the user or engineering problem;
- affected boundaries and public contracts;
- measured performance and reliability impact where relevant;
- compatibility, migration, recovery, and rollback behavior;
- diagnostics and test evidence;
- alternatives and deferred work.

## Formal Reviews

The policy, schedule, template, and records live under [ARCHITECTURE_REVIEWS.md](ARCHITECTURE_REVIEWS.md) and [architecture-reviews](architecture-reviews/README.md). The machine-readable schedule is validated in the release pipeline. A required gate cannot be satisfied by changing its status alone: the record, all required review sections, and at least one linked ADR must exist.

## Technical Debt

[TECHNICAL_DEBT.md](TECHNICAL_DEBT.md) is the canonical debt register. Debt is acceptable only when its reason, risk, dependency, and status are explicit. Review active debt at each minor milestone and in every formal architecture review. Closing an item requires evidence, not deletion from history.

## Documentation

Architecture, public APIs, user/developer guides, schema and migration notes, changelogs, release notes, and completion reports evolve with behavior. Documentation-only governance changes still pass link, JSON, architecture-review, and release validation.

## Community Contributions

Contributions follow [CONTRIBUTING.md](../CONTRIBUTING.md), [CODE_STYLE.md](../CODE_STYLE.md), architecture rules, tests, and documentation requirements. Consistency and backward compatibility take precedence over individual style preference.

## Amendment

Routine clarifications may update governance directly. Changes to stable foundations, review gates, compatibility promises, or decision authority require an ADR and review of this document, the long-term vision, and the architecture-review schedule.

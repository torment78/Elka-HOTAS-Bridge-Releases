# Architecture Decision Records

Architecture Decision Records explain consequential HOTASBridge design choices so later changes can distinguish deliberate constraints from accidental structure.

## When an ADR Is Required

Create an ADR when a change affects:

- project or subsystem boundaries;
- public plugin or scripting APIs;
- profile/schema compatibility and migration;
- scheduler or thread ownership;
- driver, native API, or third-party dependency selection;
- security, privacy, recovery, or output safety;
- replacement of an established architecture decision.

Normal implementation details, narrow bug fixes, and reversible UI styling do not need an ADR.

## Naming and Status

Use a four-digit sequence and short title:

```text
0001-use-runtime-signals.md
```

Allowed statuses are `Proposed`, `Accepted`, `Superseded`, and `Rejected`. Never rewrite the reasoning of an accepted ADR. Add a new ADR and link both records when a decision changes.

## Template

```markdown
# ADR 0001: Short Decision Title

- Status: Proposed
- Date: YYYY-MM-DD
- Decision owners: Names or team

## Context

Describe the problem, constraints, compatibility requirements, and evidence.

## Decision

State the selected approach and its boundaries.

## Alternatives

List credible alternatives and why they were not selected.

## Consequences

Record benefits, costs, risks, migration work, and follow-up items.
```

## Accepted Decisions

| ADR | Decision |
| --- | --- |
| [0001](0001-preserve-runtime-foundations.md) | Preserve working runtime foundations and evolve through adapters and published contracts. |
| [0002](0002-formal-architecture-review-gates.md) | Enforce formal reviews at major compatibility and release milestones. |
| [0003](0003-normalized-hat-and-output-plugins.md) | Normalize provider hats and keep generated mouse output behind the output-plugin boundary. |
| [0004](0004-descriptor-authoritative-mapping-behavior.md) | Make enabled mapping behavior descriptors authoritative while retaining legacy projections temporarily. |
| [0005](0005-optional-branching-graphs.md) | Persist optional branching graphs and compile them through the existing RuntimeSignal and OutputAction contracts. |

Formal review records live under [../architecture-reviews](../architecture-reviews/README.md). A satisfied milestone gate links at least one ADR; the review records evidence and findings, while the ADR records the durable decision.

## Review Process

1. Add the ADR as `Proposed` with the related code or design change.
2. Review architecture, compatibility, tests, diagnostics, and rollback effects.
3. Mark it `Accepted` only when the decision is approved and implemented.
4. Link follow-up technical debt and migration documentation.
5. Use a new ADR to supersede an accepted decision.

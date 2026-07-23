# ADR 0002: Formal Architecture Review Gates

- Status: Accepted
- Date: 2026-07-18
- Decision owners: HOTASBridge architecture owner

## Context

The project has roadmap, Project Health, feature policy, ADR guidance, and automated architecture validation, but no executable schedule tying major compatibility expansions to a formal review record. Several future systems already have foundations, making it important to distinguish retrospective acceptance from prospective authorization.

## Decision

Maintain a versioned machine-readable architecture-review schedule. Require formal review before Version 1.0, supported external plugin loading, branching Node Editor persistence or promotion, Scripting Engine promotion or default enablement, and profile schema versions beyond the latest reviewed version.

Every satisfied gate requires a review record containing the six mandatory review areas and at least one linked ADR. Release validation checks schedule structure, evidence links, current profile schema coverage, and Version 1.0 gate status.

## Alternatives

- Use informal milestone discussions only. Rejected because conclusions and evidence become difficult to reproduce.
- Make every pull request a formal architecture review. Rejected because ceremony would obscure the genuinely consequential decisions.
- Store review state only in issue tracking. Rejected because release validation and offline source history need a repository-owned record.

## Consequences

- Major changes can be blocked by missing evidence instead of reviewer memory.
- Retrospective baseline acceptance is labeled honestly and does not imply earlier pre-implementation review.
- Review records, ADRs, specification, Project Health, and roadmap require synchronization.
- Manual gates must be activated when work enters scope; this remains a governance responsibility checked during planning and review.

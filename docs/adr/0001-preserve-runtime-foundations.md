# ADR 0001: Preserve Runtime Foundations

- Status: Accepted
- Date: 2026-07-18
- Decision owners: HOTASBridge architecture owner

## Context

HOTASBridge has working hardware input, immutable RuntimeSignals, mappings, transforms, versioned profiles, centralized scheduling, Xbox and keyboard outputs, diagnostics, simulation, recovery, and extension foundations. Replacing these systems to pursue architectural uniformity would create migration and hardware regression risk without measured user value.

## Decision

Treat the Runtime Signal Engine, Mapping Engine, Transform Engine, Output Manager and plugins, Profile System, centralized scheduler, and diagnostics framework as stable foundations. Add capabilities through existing interfaces, adapters, providers, transforms, plugins, and additive versioned contracts.

A foundational replacement requires measured evidence that compatible incremental evolution is insufficient, plus migration, rollback, diagnostics, tests, a superseding ADR, and formal architecture review.

## Alternatives

- Rewrite the runtime around a new unified implementation immediately. Rejected because current behavior is hardware validated and compatibility risk is high.
- Freeze all foundation code. Rejected because performance, diagnostics, testability, and implementation quality must continue improving.
- Permit subsystem-local bypasses for speed. Rejected because bypasses undermine diagnostics, recovery, and future extension.

## Consequences

- Existing mappings, profiles, and output behavior retain priority.
- New hardware and outputs enter through provider and plugin boundaries.
- Compatibility adapters may remain while migrations are measured and tested.
- Some technical debt closes more slowly because cleanup is incremental.
- Any future replacement carries an explicit evidence and governance burden.

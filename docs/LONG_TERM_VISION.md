# Long-Term Vision

## Vision

HOTASBridge is a universal Human Interface Device signal-processing platform. It accepts signals from independent providers, normalizes them into the runtime model, processes them deterministically, and dispatches standardized output actions through isolated plugins.

HOTAS-to-Xbox translation remains a protected, supported use case. The broader architecture must also be capable of supporting flight, space, and racing simulation; accessibility devices; robotics; industrial controls; research; custom cockpit hardware; and education without replacing the runtime foundation.

## Enduring Principles

- Preserve working user behavior and profile compatibility.
- Extend through published interfaces, adapters, providers, transforms, and plugins.
- Prefer incremental migration over subsystem replacement.
- Keep runtime behavior deterministic, non-blocking, measurable, and recoverable.
- Treat diagnostics, testing, migration, and documentation as product capabilities.
- Prefer long-term maintainability over short-lived convenience.
- Keep user control explicit, especially for automation, scripting, AI, drivers, and online services.

## Stable Foundations

The following boundaries are foundational:

- Runtime Signal Engine and latest-value cache;
- Mapping Engine and runtime-only mapping state;
- Transform Engine and ordered transform contracts;
- Output actions, Output Manager, and output plugins;
- Input provider and device identity contracts;
- versioned profile, migration, and backup services;
- centralized scheduler and queue ownership;
- telemetry, stage diagnostics, health, and recovery services;
- feature policy and public extension API version negotiation.

Stable does not mean frozen. These systems may be improved behind compatible interfaces, but a replacement requires measured evidence, migration and rollback plans, an ADR, and a formal architecture review.

## Extension Test

An architectural change is healthy when:

- a new input provider can publish RuntimeSignals without changing mapping logic;
- a new output plugin can consume OutputActions without changing the Mapping Engine;
- a new transform can be registered without modifying existing transforms;
- profiles created by supported older versions remain loadable and recoverable;
- diagnostics can explain the signal path without special UI-only instrumentation;
- public APIs expose stable contracts rather than internal implementation classes.

## Deliberate Horizons

Near-term work prioritizes Version 1.0 reliability, hardware acceptance, migration rehearsal, supported runtimes, signing, installer acceptance, and soak evidence. External plugins, branching node graphs, hardened scripting, remote AI, online profile services, and cloud synchronization remain gated expansions rather than assumptions baked into the core.

Major-version targets express architectural direction, not dates. The authoritative delivery state remains [ROADMAP.md](ROADMAP.md), [PROJECT_HEALTH.md](PROJECT_HEALTH.md), and the machine-readable Project Health snapshot.

## Living Document Rules

Update this document when the product mission, stable foundations, extension model, or compatibility promise changes. Update the relevant specification chapter and create or supersede an ADR for consequential decisions. Historical ADR reasoning is never rewritten.

The source code and executable validation evidence remain the primary truth when prose and implementation differ. A discrepancy is a defect to resolve, not permission to select the more convenient account.

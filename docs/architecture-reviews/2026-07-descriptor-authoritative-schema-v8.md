# Architecture Review: Descriptor-Authoritative Profile Schema v8

- Date: 2026-07-20
- Gate ID: profile-schema-change
- Scope: Runtime-authoritative mapping behavior descriptors and additive profile schema v8 migration
- Decision owners: HOTASBridge architecture owner
- Reviewers: HOTASBridge engineering review
- Outcome: Accepted
- Related ADRs: ADR 0004
- Related issues/debt: TD-031

## Executive Summary

Schema v8 resolves the split authority between ordered transform descriptors and legacy mapping behavior fields. Every persisted mapping has one enabled generated behavior descriptor. Registered transforms execute behavior, toggle, pulse, inversion, release, repeat mode, and timing, then publish the effective result as RuntimeSignal metadata for later stages.

Legacy properties remain non-destructive migration and authoring projections. Removing them is explicitly outside this milestone.

## Evidence

- Existing RuntimeSignal, Mapping Engine, Transform Engine, OutputAction, Output Manager, scheduler, profile store, and WPF authoring boundaries are preserved.
- Focused Core validation passes 243 tests after the migration and runtime cutover; full repository validation passes 366 tests with 58.64% merged runtime line coverage (16,110/27,473).
- New regressions prove descriptor authority over stale legacy axis/repeat settings, v7-to-v8 projection, preservation of current v8 descriptor edits, Mapping Editor synchronization, Axis Curves isolation, and behavior-last migration order.
- The profile store retains exact backup-before-migration behavior.
- The machine-readable v8 schema requires one enabled generated behavior/toggle/pulse descriptor per persisted mapping.
- Debug and Release builds complete with zero warnings/errors; architecture validation reports zero findings; the schema-v8 review gate passes. WPF startup smoke evidence is recorded in the TD-031 completion report before commit.

## Performance

Behavior execution adds one registered transform call and a small bounded metadata dictionary update per mapped signal. It creates no thread, timer, queue, hardware call, or UI callback. Indexed mapping lookup and centralized output scheduling are unchanged.

## Maintainability

One RuntimeSignal metadata contract now carries effective behavior into output mapping and OutputAction creation. Generated descriptor synchronization has explicit scopes: full mapping authoring, axis-only curve authoring, one-time migration overwrite, and missing-only normal persistence.

## Public APIs

No supported external Plugin API or Script API version changes. Profile JSON advances from v7 to v8. The new synchronization method is a Core authoring boundary, not a third-party plugin contract.

## Plugin Interfaces

Output plugins still consume OutputActions and do not inspect profile mappings or legacy behavior properties. Xbox, keyboard, and mouse plugin lifecycle and isolation are unchanged.

## Runtime Diagnostics

The behavior, toggle, and pulse descriptors publish normal transform-stage diagnostics. Runtime mapping state reports the effective descriptor mode. Repeat and pulse timing cross the pipeline as immutable signal metadata.

## Backward Compatibility

Schemas v1-v7 migrate to v8 after an exact backup. During migration, only stable generated descriptor IDs are overwritten from previously authoritative legacy fields. Unknown/custom transforms, mappings, outputs, device identities, macros, and profile metadata are preserved. Current v8 descriptor edits survive normal load/save.

Descriptor-free in-memory mappings retain legacy fallback behavior. Legacy persisted properties remain until a future separately reviewed migration.

## Findings

| ID | Severity | Finding | Resolution or owner |
| --- | --- | --- | --- |
| F-201 | Decision | Enabled behavior descriptors are the runtime source of truth. | ADR 0004 |
| F-202 | Decision | Legacy fields remain projections during the UI/profile transition. | ADR 0004 and future migration |
| F-203 | Decision | Axis Curves cannot overwrite an independently edited behavior descriptor. | Focused synchronization scope |
| F-204 | Advisory | Full legacy-field removal requires direct descriptor authoring in every profile tool. | Future reviewed schema migration |

## Decision

Accepted. Profile schema v8 is approved through the existing profile-schema-change gate. Preserve non-destructive migration, descriptor order, plugin isolation, and descriptor-free fallback until compatibility retirement is separately approved.

## Follow-Up

- Record final automated validation and coverage in the TD-031 completion report.
- Retire compatibility projection fields only after all authoring surfaces use descriptor-native configuration.
- Continue physical HOTAS and generated-output validation under the existing hardware matrix.
# ADR 0004: Make Mapping Behavior Descriptors Authoritative

- Status: Accepted
- Date: 2026-07-20
- Decision owners: HOTASBridge architecture owner

## Context

Profile schemas v4-v7 persisted ordered transform descriptors but still used InputMapping.Behavior, ButtonProcessing, and PulseDuration after the configured transform chain. Generated behavior descriptors were disabled compatibility markers for direct behavior, and output action selection still read legacy fields. That split source of truth made Transform Editor changes capable of disagreeing with live mapping behavior.

Existing profiles and Mapping Editor workflows still depend on the legacy properties. Removing them immediately would invalidate older files and require broad UI migration.

## Decision

Profile schema v8 makes the generated mapping behavior descriptor the runtime authority.

- Every persisted mapping receives one enabled generated behavior descriptor with stable ID <mappingId>-behavior.
- Toggle and pulse behaviors continue to use their registered transform types; direct, inverted, release, repeat, and other modes use the registered behavior transform.
- Executed descriptors publish effective behavior, repeat timing, and pulse timing as immutable RuntimeSignal metadata consumed by output mapping and action generation.
- Legacy behavior fields remain serialized projections for old profiles and the current Mapping Editor.
- Schema v1-v7 profiles overwrite only generated descriptor IDs from the legacy fields during the one-time v8 migration; unknown/custom descriptors are preserved.
- Current schema-v8 profiles preserve descriptor edits during load/save. Explicit Mapping Editor updates synchronize the selected mapping, while Axis Curves updates only axis-processing descriptors.
- Descriptor-free in-memory mappings retain the legacy fallback until the compatibility properties are retired in a separately reviewed migration.

## Alternatives

1. Remove legacy fields in schema v8. Rejected because it would combine runtime cutover with a broad UI and profile rewrite.
2. Continue treating descriptors as mirrors. Rejected because two mutable authorities can disagree and prevents reliable node/transform editing.
3. Infer effective behavior directly from legacy fields in every consumer. Rejected because it does not advance the RuntimeSignal transform architecture.

## Consequences

Descriptor edits now determine live behavior without requiring matching legacy-field changes. Runtime diagnostics report the effective descriptor mode. Old profiles receive exact pre-migration backups through the existing profile store.

The compatibility properties remain technical debt, but their runtime role is limited to migration, authoring projection, transform setting defaults, and descriptor-free test/adapter fallback. A future schema migration may remove them only after Mapping Editor and all profile tools author descriptors directly.
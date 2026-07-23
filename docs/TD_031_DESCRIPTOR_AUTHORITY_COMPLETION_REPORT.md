# TD-031 Descriptor Authority Completion Report

Status: Complete

## Existing Implementation Assessment

| Requirement | Before TD-031 | Result |
| --- | --- | --- |
| Ordered transform descriptors | Partially implemented | Preserved and extended. |
| Axis processing descriptors | Implemented with fallback | Preserved. |
| Mapping behavior descriptor | Persisted compatibility marker | Now executes directly and is runtime-authoritative. |
| Toggle and pulse descriptors | Executed statefully | Preserved; now publish effective behavior/timing metadata and consume descriptor inversion. |
| Output mapping/action behavior | Read legacy profile fields | Consumes immutable metadata from executed descriptors, with legacy fallback only when no descriptor executed. |
| Schema compatibility | v1-v7 | Additive v1-v7 to v8 migration with exact existing backup behavior. |
| Legacy behavior properties | Runtime authority | Retained only as migration/authoring projections and descriptor-free compatibility fallback. |
| Full property retirement | Missing | Deferred to a separately reviewed future migration after all authoring surfaces are descriptor-native. |

## Change

- Advanced the profile contract to schema v8 and added the machine-readable v8 JSON Schema.
- Added one enabled generated behavior descriptor to every persisted mapping.
- Implemented behavior execution for axis inversion, digital inversion, On Release, repeat timing, and pass-through compatibility modes.
- Extended toggle and pulse transforms to publish effective mode and timing.
- Passed behavior metadata through RuntimeSignal to Output Mapping and OutputAction generation.
- Limited normal persistence to missing-descriptor repair so current v8 descriptor edits survive.
- Made Mapping Editor updates refresh all generated descriptors for the edited mapping.
- Made Axis Curves updates refresh only axis-processing descriptors, preserving independent behavior edits.
- Preserved custom transforms and behavior-last semantics when rebuilding historical pre-v4 chains.
- Updated Node Editor descriptions, active architecture/profile/mapping/transform docs, changelog, release notes, roadmap, Project Health, ADR index, and architecture-review index.

## Compatibility

- Schemas v1-v7 migrate automatically to v8.
- JsonProfileStore still creates an exact source backup before writing an on-disk migration.
- During the one-time migration, only stable generated descriptor IDs are overwritten from the previously authoritative legacy fields.
- Unknown/custom descriptors, mappings, outputs, devices, macros, metadata, and runtime-state separation are preserved.
- Schema-v8 normal load/save does not overwrite edited descriptor settings.
- Descriptor-free in-memory/test mappings retain legacy behavior fallback.
- No Plugin API, Script API, output-plugin, scheduler, device identity, or UI layout contract changed.

## Architecture

ADR 0004 records the durable descriptor-authority decision. The profile-schema-change gate has a new accepted review and is approved through schema v8. The validator reports two satisfied and four planned review gates.

The remaining legacy fields are intentionally not removed in this milestone. Their future removal requires direct descriptor authoring across Mapping Editor, comparison/merge, templates, importers, and all migration tools.

## Tests

Focused regressions cover:

- descriptor axis inversion overriding stale legacy behavior;
- descriptor repeat mode and timing overriding stale legacy settings;
- descriptor On Release behavior without the legacy output switch;
- v7-to-v8 projection and preservation of subsequent v8 descriptor edits;
- Mapping Editor refresh of the generated behavior descriptor;
- Axis Curves isolation from behavior descriptors;
- behavior-last order after preserved historical custom transforms;
- behavior transform registration and generated-chain order.

## Validation

- Restore: passed.
- Debug build: passed with 0 warnings and 0 errors.
- Release build: passed with 0 warnings and 0 errors.
- Automated tests: 366 passed, 0 failed, 0 skipped.
- Merged runtime line coverage: 58.64% (16,110/27,473).
- Architecture validator: 0 errors, 0 warnings, 0 suggestions.
- Architecture review self-test: passed.
- Architecture review schedule: passed; profile schema v8 accepted.
- Documentation/schema/artifact validation: passed.
- WPF startup smoke: passed for fresh, existing Easy/Advanced, and Safe Mode policies.
- Release readiness remains correctly blocked by 5 known release blockers and 8 pending manual checks unrelated to TD-031.

## Deferred

- Remove behavior, buttonProcessing, pulseDuration, and remaining axisProcessing fallback dependencies only through a future reviewed profile migration.
- Convert every authoring/import/comparison surface to descriptor-native configuration before that removal.
- Complete the existing physical HOTAS, output, installer, and release-acceptance checks.
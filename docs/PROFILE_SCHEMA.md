# Profile Schema

The active profile format is human-readable JSON. The machine-readable contract is [schema/hotas-profile-v9.schema.json](schema/hotas-profile-v9.schema.json).

## Version Fields

| Field | Meaning |
| --- | --- |
| schemaVersion | Structural JSON contract. Current value: 9. |
| applicationVersion | HOTASBridge build that last saved the file. |
| profileVersion | Per-profile revision, incremented after each persisted save. |

## Top-Level Shape

Profiles store stable identity and metadata, logical device groups and selected devices, calibration, mappings, macros and variables, curves/layers, output configuration, conflict policy, and migration audit data. Runtime values, output state, timers, diagnostics, and workspace layout are never profile data.

## Mapping Shape

Each mapping stores:

- stable mapping ID and name;
- source device/control identity and input type;
- enabled state, shift layer, conditions, priority, and notes;
- ordered transformChain configurations;
- output plugin, control, kind, and plugin configuration;
- normalized hat and pointer-output settings where applicable;
- an optional graph.

Schema v8 made the enabled generated behavior descriptor authoritative. Behavior, inversion, release, toggle, pulse, repeat mode, and timing execute through registered transforms. The legacy axisProcessing, buttonProcessing, behavior, and pulseDuration properties remain serialized projections until a separately reviewed removal.

## Axis Processing Shape

Axis processing stores calibration range, shared center offset, inversion, anti-deadzone, output range, and symmetric curve/deadzone/sensitivity settings.

Schema v9 also accepts the additive `useIndependentSides`, `negativeSide`, and `positiveSide` properties. Each side contains `innerDeadzone`, `outerDeadzone`, `sensitivity`, and `curve`. When `useIndependentSides` is absent or false, the runtime ignores the side objects and preserves the symmetric processing path.

Directional settings are configuration only. Current values, selected editor side, processing history, and diagnostic state are not serialized.

## Optional Graph

Schema v9 adds the nullable graph property to InputMapping. It is absent or null for ordinary linear mappings.

A graph contains graph schema version 1, graphId, enabled/linear flags, nodes, typed ports, and directed connections. Transform nodes reference configurations in the same mapping transformChain. Logic and plugin nodes own only their node settings.

The current graph contract requires one Input and one Output, rejects cycles and incompatible or missing required ports, and limits each mapping to 4,096 nodes and 16,384 connections. Unknown plugin node types are preserved but cannot activate without a registered processor.

Node positions and zoom are workspace schema v3 data and are deliberately excluded from profile JSON.

## Transform Shape

A transform stores a stable ID, registered or future type name, enabled state, and invariant-culture string settings. Unknown transform types are retained. Empty type names are validation errors.

## Macro Shape

Each macro stores stable identity, metadata, one trigger, conditions, playback mode, ordered timeline actions, optional named action groups, repeat policy, priority, and notes. Variable definitions store type and initial value. Active steps, sequence cursor/direction, waits, current variable values, held outputs, and diagnostics are runtime-only.

`Timeline` uses the existing `actions` collection. `Sequence` and `PingPong` use one to five `actionGroups`; every group has a stable ID, editable name, and nonempty ordered action collection. Existing schema-v9 files omit these optional fields and continue as Timeline macros without migration loss.

## Output Shape

Outputs store plugin ID, display name, enabled state, and plugin configuration. No live button, axis, scheduler, timer, or connection state is valid in an output configuration.

## Compatibility Policy

- Missing optional fields and explicitly null optional collections are rehydrated safely.
- Unknown JSON properties and transform configurations are preserved where supported by the serializer.
- Current graph documents are normalized without flattening branches.
- Graph-free mappings use the unchanged linear runtime.
- A newer profile schema is never downgraded or saved by an older application.
- Warnings do not block save; errors do.
- Loading an older active profile creates an exact pre-migration backup.

## Version History

| Schema | Changes |
| --- | --- |
| 1 | Original selected devices, mappings, curves, layers, and Xbox output target. |
| 2 | Metadata, profile revisions, migration audit, device groups, outputs, transforms, settings separation, validation, and health. |
| 3 | Generic mapping/output metadata, conditions, priority, notes, and conflict policy. |
| 4 | Registered transform chains, calibration, filters, diagnostics, live rebuild, and presets. |
| 5 | Macro definitions and runtime-variable definitions. |
| 6 | Searchable profile-library metadata and local package workflows. |
| 7 | Normalized hat direction/center/diagonal data and mouse pointer output settings. |
| 8 | Generated behavior descriptors become runtime-authoritative while legacy fields remain projections. |
| 9 | Optional versioned branching mapping graphs plus additive independent negative/positive axis settings; existing linear mappings and symmetric curves remain unchanged. |

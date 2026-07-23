# Node Graph Model

## Ownership

The graph model is UI-independent and belongs to Core.

| Layer | Responsibility |
| --- | --- |
| Domain | Versioned persisted graph, nodes, ports, connections, normalization, and structural validation |
| Graphs | Projection, edit sessions, templates, duplication identity, layout-independent viewport selection |
| Runtime | Bounded DAG compilation, deterministic node execution, and stage diagnostics |
| Processing | Mapping lookup, graph-plan ownership, post-graph output mapping, and fault isolation |
| App | WPF presentation, drag/pan/zoom, port interaction, and workspace layout callbacks |

The graph accepts and produces RuntimeSignal values. It does not communicate with hardware, Windows APIs, or output backends.

## Persisted Shape

An optional mapping graph contains:

- graph schema version;
- stable graph ID;
- enabled and linear-projection flags;
- nodes;
- typed ports;
- directed connections.

A node stores its ID, kind, type, optional transform configuration ID, enabled state, settings, and ports. A connection stores stable source/target node and port IDs. Position and zoom are excluded.

Graph schema v1 belongs inside profile schema v9. A future graph-schema change can evolve independently from the enclosing profile schema.

## Signal Types

Ports use four compatibility types:

| Type | Meaning |
| --- | --- |
| Any | Pass-through or extension point accepting any RuntimeSignal |
| Analog | Continuous numeric value |
| Digital | Active/inactive value |
| Directional | Normalized hat or direction value |

A connection is valid when source and target types match or either endpoint is Any.

## Compilation

When mappings load or change:

1. Profile graph normalization restores missing default ports.
2. Structural validation checks identities, limits, ports, types, required inputs, cycles, and reachability.
3. Registered plugin processors are resolved.
4. A stable topological order and incoming-connection table are compiled.
5. Invalid plans remain diagnosed and are excluded from runtime lookup.

Graph-free mappings keep the unchanged ordered pipeline. Graph mappings execute their plan and feed the resulting RuntimeSignal into the existing compatibility and Output Mapping stages.

## Runtime Rules

- Exactly one mapping Input node and one Output node are supported.
- Branches may split and merge between them.
- Each target port has at most one incoming connection; reconnect replaces its previous source.
- Logic execution is deterministic.
- Transform nodes reuse registered Transform Engine implementations and runtime state.
- Plugin nodes run only registered internal processors.
- Exceptions become invalid diagnostic signals and cannot terminate the engine.
- Every node publishes duration and current input/output telemetry.

## Editing And History

MappingGraphEditSession snapshots both the transform chain and optional graph. Add, remove, connect, disconnect, enable, settings, template, and transform-order operations participate in bounded undo/redo. Transform clipboard data is deep-cloned and receives new configuration IDs.

Mapping duplication re-identifies the mapping, graph, nodes, connections, and transform configurations so copies share no runtime or editing identity.

## Current Boundaries

The following remain deferred:

- multiple output nodes;
- cross-mapping graphs;
- feedback loops;
- external plugin packages;
- script nodes;
- remote or AI-generated graphs;
- online graph/template repositories.

These additions require their own architecture and compatibility review.

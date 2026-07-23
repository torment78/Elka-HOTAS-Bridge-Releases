# ADR 0005: Optional Branching Graphs Compile Through Existing Runtime Contracts

- Status: Accepted
- Date: 2026-07-20
- Decision owners: HOTASBridge architecture
- Related gate: `node-editor-expansion`
- Related debt: TD-032

## Context

The Beta Node Editor projects one `InputMapping` as a linear input, ordered transform chain, and output. TD-032 requires durable branching connections, logic and plugin-node extension points, direct port editing, independent workspace positions, and large-graph rendering without replacing the established RuntimeSignal, Mapping Engine, OutputAction, or profile migration foundations.

Persisting only canvas state would not describe behavior. Replacing every linear mapping with a graph would create unnecessary migration risk and would make the traditional Mapping Editor dependent on a more complex representation that most mappings do not need.

## Decision

1. `InputMapping` remains the profile-owned configuration and gains one optional, versioned graph definition.
2. A missing or disabled graph continues through the existing ordered `TransformChain` exactly as before.
3. A graph contains stable node, port, and connection IDs. Version 1 has one mapping input and one mapping output; arbitrary acyclic branches may split and merge between them.
4. Transform nodes reference the mapping-owned transform configurations. The traditional and graph editors therefore continue to edit one source of transform settings.
5. Logic nodes execute deterministic RuntimeSignal operations. Plugin nodes execute only through registered graph-node processors; this decision does not authorize external assembly loading.
6. Invalid, cyclic, incompatible, unknown, or over-limit graphs are diagnosed and disabled at the mapping boundary. They never terminate the runtime.
7. The graph executor publishes the resulting RuntimeSignal into the existing output-mapping stage, which continues to create ordinary immutable OutputActions.
8. Existing schema-v8 profiles migrate additively to schema v9 without materializing graphs. A graph is created only by explicit graph editing or conversion.
9. Node positions, zoom, and viewport preferences remain workspace state and are not part of profile behavior.
10. Linear-representable graphs may synchronize to the traditional editor. A branching graph remains graph-owned and is never silently flattened.

## Alternatives

- Replace every mapping with a persisted graph: rejected because it adds migration and authoring risk without user value for ordinary mappings.
- Serialize WPF node view models: rejected because it couples behavior to presentation and breaks non-WPF consumers.
- Execute graph nodes inside output plugins: rejected because it bypasses Mapping Engine and RuntimeSignal boundaries.
- Load third-party graph assemblies now: rejected because external plugin trust and isolation remain TD-044.

## Consequences

- Profiles gain an additive schema-v9 property and require migration, schema, comparison, validation, and backup coverage.
- Runtime graph execution adds bounded DAG compilation and per-node diagnostics while preserving the linear hot path.
- Branching graphs cannot be fully edited by the traditional linear editor; the UI must state that ownership explicitly and preserve data.
- Workspace schema advances independently to store graph presentation state.
- Multiple output nodes, cross-mapping graphs, external plugin packages, scripts as nodes, and arbitrary loops remain outside this decision.

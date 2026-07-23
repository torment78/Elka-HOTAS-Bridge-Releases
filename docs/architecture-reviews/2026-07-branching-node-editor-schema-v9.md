# Branching Node Editor Architecture Review

- Date: 2026-07-20
- Gate: `node-editor-expansion`
- Proposed profile schema: v9
- Proposed workspace schema: v3
- Decision: Accepted and implemented
- ADR: `docs/adr/0005-optional-branching-graphs.md`

## Scope

Review the TD-032 expansion from a linear projection to an optional persisted branching graph with direct typed ports, deterministic logic, internal plugin-node extension points, independent workspace positions, and viewport-based rendering.

## Existing Foundation

| Requirement | Existing state | Review decision |
| --- | --- | --- |
| RuntimeSignal and OutputAction boundaries | Stable and tested | Preserve |
| Linear transform execution | Stable and descriptor-authoritative | Keep as default path |
| Node projection and validation | Implemented | Extend rather than replace |
| Shared InputMapping ownership | Implemented | Preserve |
| Undo, redo, templates, diagnostics | Implemented | Reuse |
| Branching persistence/runtime | Missing | Add optionally |
| Direct port editing | Missing | Add over stable ports |
| Workspace positions | Runtime-only | Persist outside profiles |
| Logic/plugin nodes | Reserved only | Add deterministic Core logic and registered internal extension points |
| Large-graph virtualization | Missing | Add viewport culling without touching runtime |

## Review Areas

### Performance

Graph compilation occurs when mappings are loaded or edited, never for every UI frame. Execution uses a bounded acyclic plan. Node and connection limits reject pathological documents. The WPF surface renders only viewport-adjacent nodes and connections once a graph crosses the virtualization threshold.

### Maintainability

Persistence models live in Core Domain, projection/editing in Core Graphs, execution in Core Runtime/Processing, and WPF interaction in the App. Workspace positions use the existing workspace store. Linear and graph paths share transform registrations and output mapping.

### Public APIs

No external public Plugin SDK promise is introduced. Persisted graph schema v1 and internal graph-node processor interfaces are versioned separately from profile schema v9.

### Plugin Interfaces

Only registered in-process processors supplied by the application composition may execute plugin graph nodes. Unknown types remain visible and invalid. External package loading, trust, permissions, and isolation remain blocked by TD-044.

### Runtime Diagnostics

Every compiled graph node exposes current input/output, duration, enabled state, timestamp, and warning/error state through existing telemetry. Invalid graphs are reported through mapping status and Profile Health.

### Backward Compatibility

Schema-v8 and earlier profiles keep their transform chains and do not receive graph data automatically. Graph-free mappings use the unchanged linear runtime. Exact pre-migration backups remain mandatory. Branching graphs are not silently downgraded or flattened.

## Conditions

- Add deterministic migration and round-trip tests.
- Keep the existing linear execution path and editor operational.
- Reject cycles, incompatible ports, missing required ports, unknown processors, and safety-limit violations before activation.
- Keep node layout outside profile JSON.
- Run the architecture validator, full tests, Debug/Release builds, schema validation, and WPF startup smoke before completion.

## Deferred

- Multiple output nodes and cross-mapping graphs.
- Loop/feedback nodes.
- External plugin packages and script nodes.
- Online graph/template repositories.
- Automatic AI graph generation.
## Validation Evidence

- Debug and Release solution builds complete with zero warnings and zero errors.
- 378 automated tests pass with no failures or skips.
- Merged runtime line coverage is 56.77 percent (17,256 of 30,397 lines).
- Architecture validation reports no errors, warnings, or suggestions.
- The architecture-review schedule reports three satisfied and three planned gates and accepts profile schema v9.
- The isolated WPF smoke opens the Node Editor in Advanced mode, finds the graph and both node palettes, and completes coordinated shutdown.
- Existing graph-free mappings, v8-to-v9 migration, graph round trip, invalid processor isolation, direct ports, undo/redo, duplication identity, profile comparison/merge, workspace persistence, and viewport culling have focused regressions.

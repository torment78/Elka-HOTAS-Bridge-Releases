# TD-032 Branching Node Editor Completion Report

## Scope

TD-032 expands the existing linear Node Editor incrementally. InputMapping remains the single source of truth, existing graph-free mappings retain their established runtime, and all graph behavior continues through RuntimeSignal, Mapping Engine, Output Mapping, OutputAction, and Output Manager boundaries.

## Requirement Assessment

| Requirement | Previous state | Result |
| --- | --- | --- |
| Linear mapping projection and editing | Implemented | Preserved |
| Versioned branching persistence | Missing | Complete in profile schema v9 |
| Stable typed ports/direct editing | Partial display only | Complete |
| Logic nodes | Reserved | AND, OR, NOT, and Compare complete |
| Plugin-node extension point | Reserved | Registered internal processor boundary complete |
| Graph runtime execution | Missing | Bounded deterministic DAG compiler/runtime complete |
| Workspace positions/zoom | Runtime-only | Complete in workspace schema v3 |
| Large-graph rendering | Missing | Viewport culling above 75 nodes complete |
| Shared diagnostics | Transform foundation | Per graph node complete |
| Migration/comparison/duplication | Linear only | Graph-safe and tested |

## Implementation

- Added optional versioned graph, node, port, and connection configuration under InputMapping.
- Preserved the graph-free ordered transform path; existing mappings are not materialized as graphs during migration.
- Added structural normalization and validation for identity, ports, types, required inputs, cycles, reachability, limits, and processor availability.
- Added MappingGraphRuntime compilation and execution with deterministic topological order, registered transforms, logic processing, internal plugin processors, and per-node telemetry.
- Routed graph results through the existing post-transform compatibility and Output Mapping stages.
- Expanded MappingGraphEditSession with graph-aware snapshots, direct connect/disconnect, node settings/enabled state, deletion, and undo/redo.
- Added typed WPF ports, connection selection, logic/plugin palettes, persisted layout callbacks, minimap retention, and viewport rendering.
- Re-identified graph/node/connection/transform IDs during mapping duplication.
- Included graph topology in profile comparison and merge.
- Advanced workspace layout storage to schema v3 and profile storage to schema v9.
- Recorded ADR 0005 and satisfied the node-editor-expansion and profile-schema-change review gates.

## Compatibility

Schema-v8 and earlier profiles migrate additively after the existing exact-backup rule. Graph-free mappings remain graph-free and execute the unchanged linear pipeline. Branching graphs are never silently flattened. Runtime values and node positions are not persisted in profiles.

Unknown graph plugin nodes remain visible but invalidate only their mapping. This milestone does not load external assemblies and does not change the supported external Plugin API or Script API.

## Tests

Focused regressions cover:

- linear materialization and typed ports;
- branching logic execution through Mapping Engine;
- unknown processor isolation;
- cycle rejection;
- v8-to-v9 migration and graph-free preservation;
- graph/transform identity after mapping duplication;
- direct connection replacement and undo/redo;
- logic settings and deletion history;
- graph topology comparison and merge;
- workspace layout round trip;
- large-graph viewport culling and zoom.

## Validation

- Restore: passed.
- Debug build: passed with zero warnings and zero errors.
- Release build: passed with zero warnings and zero errors.
- Automated tests: 378 passed, zero failed, zero skipped.
- Merged runtime line coverage: 56.77 percent (17,256/30,397).
- Architecture validator: zero errors, zero warnings, zero suggestions.
- Architecture review self-test: passed.
- Architecture review schedule: passed; three gates satisfied, three planned, profile schema v9 accepted.
- JSON schemas and release artifacts: passed.
- WPF startup smoke: passed for fresh, existing Easy/Advanced including Node Editor, and Safe Mode.
- Release readiness remains blocked only by the existing physical hardware, signing/clean-machine, retained-build migration, runtime-matrix, Version 1.0 review, and soak evidence outside TD-032.

## Deferred

- Multiple output nodes and cross-mapping graphs.
- Feedback loops.
- External plugin packages and untrusted graph processors.
- Script nodes and scripting hardening tracked by TD-033.
- AI graph generation and online graph/template services.

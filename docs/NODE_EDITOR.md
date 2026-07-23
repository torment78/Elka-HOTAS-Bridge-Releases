# Visual Node Editor

## Status

The Visual Node Editor is a Beta feature enabled by the node-editor feature flag. It edits the same profile-owned InputMapping used by Mapping Editor, Transform Editor, Mapping Explorer, and the Mapping Engine.

Ordinary mappings remain linear and graph-free. A persisted graph is created only when graph editing requires one. This keeps existing profiles and the traditional editor on the established path while allowing an individual mapping to opt into branches.

## Workspace

The page contains:

- a mapping selector and live/zoom controls;
- searchable transform, template, logic, and registered plugin-node palettes;
- a pannable, zoomable graph canvas with a minimap;
- direct typed input and output ports;
- selection settings, connection controls, validation, undo, and redo.

Select an output port and then a compatible input port to create or replace that input connection. Select a connection and use Disconnect to remove it. Cycles and incompatible signal types are rejected before the profile changes.

## Shared Model

InputMapping remains the source of truth.

- Transform nodes reference mapping-owned transform configurations.
- Linear edits rebuild the ordinary ordered transform chain.
- Branching edits preserve the optional graph and synchronize referenced transform nodes.
- Mapping changes rebuild only the affected runtime mapping.
- Profile comparison, merge, duplicate, save, migration, and health validation include graph topology.
- A branching graph is never silently flattened by a traditional editor.

## Node Types

| Type | Current behavior |
| --- | --- |
| Input | Publishes the selected control RuntimeSignal. Exactly one is required. |
| Transform | Executes one registered mapping transform configuration. |
| Logic | Deterministic AND, OR, NOT, or Compare processing. |
| Plugin | Executes only a graph-node processor registered by application composition. |
| Output | Passes the graph result to the existing output-mapping stage. Exactly one is required. |

The only built-in plugin node is Core Pass-Through. Unknown plugin node types remain visible, produce diagnostics, and disable only their mapping. External assembly loading is not enabled.

## Layout Persistence

Node positions and zoom are workspace presentation state, not profile behavior. Workspace schema v3 stores a bounded layout per profile/mapping pair. Layout writes are debounced while dragging. Reset restores deterministic automatic positions without changing mapping behavior.

## Large Graphs

Graphs with up to 75 nodes render in full. Larger graphs use viewport-plus-margin culling for the primary canvas while the minimap retains the complete topology. Runtime execution is independent of WPF rendering.

Persisted safety limits are 4,096 nodes and 16,384 connections per mapping. Documents above those limits are invalid and never activate.

## Validation

The editor and Profile Health report expose:

- duplicate or missing node, port, and connection IDs;
- missing transform configurations;
- unsupported logic or unregistered plugin nodes;
- missing required ports;
- incompatible signal types;
- cycles;
- a missing input-to-output path;
- graph size limits;
- ordinary mapping-definition and output-conflict findings.

Invalid mappings remain in the profile for repair. They do not execute and do not stop other mappings.

## Live Diagnostics

Input values come from the Runtime Signal Cache. Transform, logic, plugin, and output values use shared stage telemetry. Each active node can display its current value, execution duration, activity, last update, warning, and error state. The UI never reads hardware or calls output plugins directly.

## Compatibility

Profile schema v9 adds an optional graph property. Schema-v8 and earlier profiles migrate without graph materialization and continue through the existing linear runtime. Workspace schema v3 is independent from profiles. Downgrade does not flatten a branching graph; use a compatible build to edit it.

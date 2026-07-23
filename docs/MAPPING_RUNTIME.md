# Mapping Runtime

## Ownership

| Component | Owns |
| --- | --- |
| Profile | Mapping configuration, transforms, conditions, priority, conflict mode |
| Runtime mapping coordinator | Immutable profile/device context, held controls, active layer, RuntimeSignal queueing, OutputAction dispatch |
| Mapping snapshot | Validated/indexed references to current profile mappings |
| Runtime mapping state | Current/previous value, toggle, pulse, PWM/repeat state, activation time |
| Contribution table | Latest active output contribution per mapping |
| Output Manager | Generated Xbox state and backend routing |
| Output backend | Driver/API-specific output submission |

No runtime state is persisted to profile JSON.

## Mapping Lifecycle

1. A profile loads and migrates to schema v9.
2. `UpdateMappings` validates definitions and builds the device/control index.
3. `RuntimeMappingCoordinator` queues each accepted `RuntimeSignal` on the single-reader Runtime Signals lane.
4. The coordinator updates held controls, resolves an immutable active profile/layer/device context, and invokes the Mapping Engine.
5. A graph-free mapping executes the ordered signal pipeline; an enabled graph executes its validated topological plan.
6. The output-mapping stage updates toggle/pulse runtime state.
7. The engine creates output actions and updates active contributions.
8. Conflict policy selects one action per affected output target.
9. The coordinator queues the batch on the Output Actions lane; `OutputManager` routes it and updates the Xbox compatibility state.

## Runtime State

`RuntimeMappingStateStore` is keyed by mapping ID and exposes read-only snapshots. The engine alone mutates:

- current and previous output values;
- last change/output/activation timestamps;
- active transform state;
- toggle state;
- pulse expiry;
- PWM/repeat placeholders;
- active state.

When a new mapping snapshot removes an ID, its runtime state is reconciled out.

## Contributions

An active contribution represents a mapping's current claim on an output target. Axis actions remain contributions even at zero. Button/key/PWM releases remove the mapping contribution.

Conflict resolution uses all live contributions for the affected target, not only actions from the current input event. This guarantees deterministic behavior when different controls change at different times.

Live edits return transition actions from `UpdateMappings`. The app submits them immediately while mapping is running, preventing stuck buttons or axes.

## Layers And Held Controls

`RuntimeMappingCoordinator` tracks held non-analog controls by plain control ID and qualified `deviceId|controlId` under a private state lock. A non-default shift layer with a held activator becomes active; otherwise the profile default layer is used. Read-only snapshots let macro/script integration inspect the same active layer without sharing a mutable `HashSet`. A context change prunes contributions from mappings whose layer or conditions no longer match.

Toggle-activated shift layers and variable/script conditions remain future extensions. Mapping-level toggle conditions already read the runtime mapping state store.

## Output Manager

`IOutputManager` consumes action batches. The initial implementation:

- serializes dispatch with a semaphore;
- reduces Xbox actions into one state;
- submits at most one Xbox update per action batch;
- reports unsupported plugin/action families once through structured logging;
- publishes output telemetry and diagnostics;
- neutralizes and disconnects through the existing virtual-gamepad backend.

Keyboard and PWM actions route through Output Manager to the Windows SendInput plugin. The Mapping Engine never calls Windows APIs directly.

## Live Editing

The Mapping Explorer and editor call the same rebuild path after create, update, duplicate, enable, or disable. Snapshot replacement is atomic. Existing input readers and the virtual controller remain connected.

## Determinism

- Lookup is case-insensitive by stable device/control IDs.
- Configured source order is captured when the snapshot is built.
- Priority ties use source order.
- One resolved action is emitted per affected plugin/control target.
- Invalid signals and mappings cannot terminate the engine.

## Threading

Input providers publish immutable RuntimeSignals independently of the WPF dispatcher. The coordinator submits every accepted signal to the scheduler's single-reader Runtime Signals lane and submits resulting actions to the ordered Output Actions lane. Mapping snapshots are replaced atomically, mapping evaluation/live rebuilds share the engine lock, coordinator state snapshots are lock-protected, and UI values remain separately coalesced.

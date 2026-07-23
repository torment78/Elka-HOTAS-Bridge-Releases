# Output Plugins

## Interface

Every output plugin implements `IOutputPlugin`, which extends the shared `IHotasBridgePlugin` identity/manifest contract:

| Member | Responsibility |
| --- | --- |
| `PluginId` / `DisplayName` | Stable routing identity and user-facing name. |
| `Manifest` | Version, author, API range, categories, dependencies, entry metadata, and optional signature metadata. |
| `InitializeAsync` | Accept shared scheduler, telemetry, and log context; allocate non-running resources. |
| `StartAsync` | Begin producing OS-visible output. |
| `ProcessAsync` | Consume a batch of standardized `OutputAction` values. |
| `ResetAsync` | Cancel timers and neutralize/release all owned output. |
| `StopAsync` | Reset and stop the plugin. |
| `GetDiagnostics` | Return an immutable runtime snapshot. |
| `DisposeAsync` | Release native and managed resources. |

Plugins must not read input devices, perform mapping lookup, mutate profiles, or interpret raw HID data.

## SDK Catalog

`XboxOutputPlugin` and `KeyboardOutputPlugin` publish internal Output-category manifests. `OutputManager.PluginCatalog` exposes the same read-only catalog that future diagnostics and Plugin Manager UI can consume. Lifecycle transitions are reported as Loaded, Initialized, Running, Restarting, Failed, Stopped, and Disposed without changing output diagnostics.

Startup also discovers `%LOCALAPPDATA%\HOTASBridge\Plugins\**\plugin.json`. Discovery validates metadata only. External assemblies remain unloaded in Version 2, even when a manifest is compatible.

See `PLUGIN_SDK.md` and `PLUGIN_API.md` for compatibility and dependency rules.

## Output Actions

Supported action types include:

- Xbox analog set, button press, and button release;
- keyboard press, release, hold, toggle, and pulse;
- start/stop PWM;
- start/stop repeat;
- generic plugin value for future plugins.

Every action carries mapping identity, plugin/control target, value, timestamp, priority/order, active contribution state, and invariant-culture configuration.

## Xbox Plugin

`XboxOutputPlugin` filters `PluginId=xbox360`, reduces the complete action batch through `XboxOutputActionReducer`, and calls the preserved `IVirtualGamepadOutput.UpdateAsync` once. Its diagnostics include connection/driver health, current sticks/triggers/buttons, rate, last output, and cumulative backend failure evidence.

ViGEm-specific calls remain exclusively in `VirtualXboxOutputService` inside `HOTASBridge.Output`. An internal session boundary isolates native client/controller ownership for deterministic tests; it is not part of the public plugin API.

## Keyboard Plugin

`KeyboardOutputPlugin` filters `PluginId=keyboard` and injects virtual-key events through `WindowsSendInputKeyboardInjector`.

Control IDs may be a single key or combination:

```text
Space
Ctrl+Shift+K
Alt+F4
LWin+R
0x7B
```

Supported names include letters, digits, F1-F24, common modifiers, navigation keys, editing keys, lock keys, Windows keys, and volume keys. Numeric virtual-key codes may be decimal or `0x` hexadecimal.

Key combinations are reference-counted by virtual key. If two active mappings share `Ctrl`, releasing one mapping does not release `Ctrl` until the other mapping also releases it. Combination release order is reversed.

### Timed Configuration

| Setting | Meaning | Default |
| --- | --- | --- |
| `pulseMilliseconds` | Pulse/repeat key-down duration | 80 ms pulse, 30 ms repeat pulse |
| `repeatMilliseconds` | Repeat interval | 120 ms |
| `pwmDutyCycle` | Fraction of period held down | Action value |
| `pwmFrequencyHz` | PWM cycles per second | 10 Hz |

Pulse, repeat, and PWM jobs use the shared `IOutputScheduler`. Full PWM duty holds the key continuously; zero duty releases it. Stop/reset cancels jobs and releases the target.

Chapter 10 expands this contract with mapping-owned cycle/threshold/phase settings, bipolar direction keys, captured scan codes, and per-mapping diagnostics. See `KEYBOARD_OUTPUT.md` and `PWM_ENGINE.md`.
### SendInput Boundary

`IKeyboardInputInjector` isolates native injection for testing. Production uses `user32!SendInput` with keyboard input records and extended-key flags where required. Tests use a fake injector and never type into the user's desktop.

## Diagnostic Contract

`OutputPluginDiagnosticSnapshot` exposes common fields plus optional typed state:

- Xbox: full `XboxState`, active buttons, backend connection/submission/cleanup counters, and last backend failure;
- Keyboard: held keys, active PWM outputs, repeat timers, and keys/sec.

Runtime errors are bounded to the ten latest messages. The manager merges plugin-local and isolation-boundary errors. Backend counters are immutable snapshots and remain independent of WPF.

## Adding A Plugin

1. Implement `IOutputPlugin` without referencing input providers or WPF.
2. Publish a schema-v1 manifest with stable lowercase ID and supported API range.
3. Consume only actions for that ID.
4. Use the shared scheduler for timed work.
5. Make reset idempotent and comprehensive.
6. Return immutable diagnostics and contain native resource ownership.
7. Add lifecycle, compatibility, reset, isolation, and action tests.
8. Register an internal plugin in application composition.

External manifest discovery is active. External binary loading remains deferred until package, signature, permission, and load-context policy is implemented.

## Current Internal Outputs

| Plugin ID | Generated state | OS boundary | Timed work |
| --- | --- | --- | --- |
| `xbox360` | Axes, triggers, D-pad, buttons | Preserved ViGEm backend | Shared output scheduler/update batching |
| `keyboard` | Keys, modifiers, repeat, PWM | Keyboard SendInput | Shared scheduler |
| `mouse` | Relative movement, five buttons, vertical/horizontal wheel | Mouse SendInput | One shared movement job plus keyed wheel repeats |

All three implement the same initialize/start/process/reset/stop/dispose lifecycle and expose immutable diagnostic snapshots. Output Monitor filters panels using enabled profile output configurations and active mappings, then renders the corresponding Xbox, visual keyboard, or visual mouse state.

# Script API

## Compatibility Contract

The public contract lives in `HOTASBridge.ScriptApi.dll`. It does not reference Core, Infrastructure, App, input/output implementations, WPF, or MoonSharp. Scripts and future language/isolation hosts consume interfaces and immutable DTOs rather than implementation classes.

Script API `1.0` is versioned independently from the application. A script declares `-- hotas:api=1.0` or `requiredApiVersion` in a package manifest. Unsupported major versions are disabled before runtime creation.

## Host Interfaces

| Interface | Purpose |
| --- | --- |
| `IScriptSignals` | Read immutable current signal snapshots. |
| `IScriptVariables` | Read/update typed Local, Global, or Session values. |
| `IScriptOutputs` | Submit validated output requests through OutputActions. |
| `IScriptProfiles` | Read profile/device context and request profile or mapping changes. |
| `IScriptScheduler` | Schedule and cancel callbacks on the central scheduler. |
| `IScriptNotifications` | Request application notifications. |
| `IScriptLogger` | Write structured script log entries. |
| `IScriptHost` | Aggregate services for a language engine. |
| `IScriptCatalog` | Discover definitions without executing them. |
| `IScriptEngine` | Own lifecycle, publication, and immutable diagnostics. |
| `IScriptExecutionPolicy` | Decide whether trust/isolation metadata permits execution. |
| `IScriptWorkspaceService` | Validate and atomically save restricted local sources. |

Public models include `ScriptValue`, snapshots, `ScriptEvent`, `ScriptDefinition`, `ScriptResourceLimits`, trust/signature/isolation enums, and diagnostics snapshots.

## Permissions

Every Lua operation checks one declared `ScriptPermission`:

| Permission | Lua operations |
| --- | --- |
| `ReadSignals` | `get_signal`, `signals` |
| `ReadVariables` | `get_variable` |
| `WriteVariables` | `set_variable` |
| `EmitOutput` | `emit_output` |
| `ReadProfiles` | `active_profile`, `devices` |
| `ActivateProfiles` | `activate_profile` |
| `ModifyMappings` | `set_mapping_enabled` |
| `Schedule` | `schedule`, `cancel_timer` |
| `Notify` | `notify` |
| `Log` | `log` and script `print` routing |

A missing permission raises a script runtime error before the host adapter is called. Buffered commands from that failed invocation are discarded.

## Lua Surface

| Function | Result |
| --- | --- |
| `hotas.api_version` | Current public API version. |
| `hotas.get_signal(device_id, control_id)` | Latest signal table or `nil`. |
| `hotas.signals()` | Current signal tables. |
| `hotas.get_variable(name, scope)` | Typed value or `nil`. |
| `hotas.set_variable(name, value, scope)` | Update a typed runtime value. |
| `hotas.emit_output(plugin_id, control_id, action_type, value, priority)` | Buffer an OutputAction request. |
| `hotas.active_profile()` | Current profile table or `nil`. |
| `hotas.devices()` | Current device tables. |
| `hotas.activate_profile(profile_id)` | Buffer profile activation. |
| `hotas.set_mapping_enabled(mapping_id, enabled)` | Buffer mapping-state change. |
| `hotas.notify(message)` | Buffer UI notification. |
| `hotas.log(level, message)` | Write a structured log entry. |
| `hotas.schedule(timer_id, delay_ms, repeat)` | Buffer central-scheduler timer creation. |
| `hotas.cancel_timer(timer_id)` | Buffer cancellation of a script-owned timer. |

Arguments must satisfy type, finite-number, string-length, command-count, timing, and output validation before requests cross the host boundary.

## Event Data

Every delivered event table contains:

```text
kind, timestamp, mapping_id, macro_id, timer_id, data
```

Signal events also contain `event.signal`:

```text
signal_id, type, device_id, device_name, control_id, control_name,
raw_value, normalized_value, value, previous_value, state, quality,
timestamp, metadata
```

Profile/device events contain bounded immutable tables. New optional fields may be added in API 1.x; existing field meaning will not change within the same major version.

## Diagnostics

`ScriptDiagnosticSnapshot` includes lifecycle, execution count, CPU duration, total managed allocation estimate, last event/error, pending events, permissions, trust, signature, isolation, allocation quota, peak invocation allocation, and rejected-event count.

## Versioning Rules

- API major changes require explicit version negotiation.
- API minor changes may add optional services, fields, functions, permissions, or diagnostics.
- Internal classes and application versions are not part of the public Script API.
- Profile, package-schema, and Script API versions are independent.
- Deprecated members require documentation and a migration period.

Assembly-reference tests protect this boundary from accidental internal dependencies.

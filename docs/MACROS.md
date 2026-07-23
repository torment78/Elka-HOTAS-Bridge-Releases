# Macros

## Purpose

A macro combines one trigger, ordered output actions, optional conditions, playback and repeat policies, and runtime-only execution state. Macros are stored in the active profile and are independent from ordinary mappings.

The current Beta milestone supports Easy and Advanced visual authoring, physical keyboard/mouse recording, single-macro import/export, profile persistence, and live debugging. Both editor modes and JSON persistence use the same profile-owned model.

## Profile Shape

```json
{
  "macroVariables": [
    {
      "name": "gearArmed",
      "type": "Boolean",
      "initialValue": "false",
      "description": "Session-only landing gear state"
    }
  ],
  "macros": [
    {
      "macroId": "5c8f17be-6a14-4e2f-8925-886b2e0104fd",
      "name": "Landing Gear",
      "isEnabled": true,
      "trigger": {
        "type": "ButtonPress",
        "deviceStableId": "stick-id",
        "controlId": "button-7"
      },
      "conditions": [],
      "actions": [
        { "id": "set-state", "type": "SetVariable", "variableName": "gearArmed", "value": "true" },
        { "id": "press-g", "type": "PressKeyboardKey", "controlId": "71" },
        { "id": "wait", "type": "Delay", "duration": "00:00:00.050" },
        { "id": "release-g", "type": "ReleaseKeyboardKey", "controlId": "71" }
      ],
      "repeatMode": "Once",
      "repeatCount": 1,
      "maximumIterations": 1000,
      "priority": 0
    }
  ]
}
```

Enum values are serialized as strings by the profile store. Durations use the standard .NET `TimeSpan` JSON representation.

`playbackMode` is `Timeline`, `Sequence`, or `PingPong`. Timeline actions remain in `actions`. Sequence and Ping-pong use one to five named `actionGroups`, each containing its own ordered `actions` collection.

Sequence advances `1, 2, 3, ..., N, 1` on repeated trigger activations. Ping-pong advances `1, 2, 3, ..., N, N-1, ..., 1, 2` without repeating either endpoint. The active group cursor is runtime state and is never persisted.

## Trigger Configuration

| Trigger | Required configuration |
| --- | --- |
| Button press/release | Device stable ID and control ID |
| Axis threshold | Device/control, comparison, threshold |
| Axis zone entry/exit | Device/control, minimum, maximum |
| Encoder rotation | Device/control and direction `-1`, `0`, or `1` |
| Switch position | Device/control and numeric position |
| Timer | Interval of at least 10 ms |
| Profile loaded | No source control |
| Device connected/disconnected | Optional device stable ID filter |

## Action Configuration

Xbox, keyboard, mouse-button, and wheel actions require an output control ID. Relative mouse movement stores bounded X/Y deltas. Xbox axis values must be finite and between -1 and 1. Delay actions require a positive duration. Variable actions require a declared variable. Mapping/profile commands require a target GUID. Notifications require non-empty text.

Keyboard control IDs use the same output-plugin convention as keyboard mappings. The macro engine does not interpret Windows input directly.

Toggle Xbox, keyboard, and mouse actions alternate between held and released state on each execution. The first execution holds the target down and the next execution releases it. A standalone Down/Press action also remains held after successful macro completion until a matching Up/Release action, profile reconfiguration, runtime stop, or output reset. Cancelling or failing a macro still releases outputs acquired by that unfinished execution.

A held keyboard output repeats only its main key after the configured Windows keyboard delay and at the configured Windows repeat rate; chord modifiers remain down. This uses the centralized output scheduler and applies equally to toggle holds and standalone keyboard Down actions.

When an active keyboard or mouse macro presses or toggles the same host control used as its trigger, HOTASBridge owns that physical press and suppresses its original down, repeat, and up events. Different output controls do not claim the trigger. Injected HOTASBridge events remain filtered from host trigger monitoring, preventing recursive activation.

## Validation

Profile Health reports macro errors without deleting definitions. Validation covers:

- missing/duplicate macro IDs, action IDs, and variable names;
- invalid trigger source, range, direction, or timer interval;
- missing or unsupported output configuration;
- invalid variable values and references;
- missing mapping/profile targets;
- invalid repeat policy or unsafe unbounded loops.

Critical errors block profile save through the existing profile validation boundary. Disabled macros produce an informational warning and remain persisted.

## Visual Macro Editor

Macro Editor opens in Easy mode. Easy mode provides name/description, learned trigger input, Timeline/Sequence/Ping-pong playback, one-to-five named steps, recording, quick key/click/toggle/delay insertion, ordered action editing, and repeat selection. Keyboard, mouse, and Xbox actions share the normal Output Manager path.

Select the trigger field, then press a HOTAS button, keyboard key, mouse button, or keyboard/mouse chord such as `Ctrl+A` or `Ctrl+LeftButton`. The detected source is shown immediately. `Escape` cancels trigger learning. Learned keyboard and mouse transitions are normalized as RuntimeSignals and use the same Macro Engine trigger path as HOTAS controls; they do not bypass mapping runtime state or output management.

Advanced mode retains conditions, variables, timer/threshold/zone trigger configuration, mapping/profile commands, notes, priority, iteration safeguards, and all low-level action settings. Switching modes changes only the presentation; both edit the same `MacroDefinition` object.

A newly added macro is intentionally disabled. Review its trigger and actions, resolve the validation summary, enable it, and use **Save Profile**. When mapping is already active, valid edits refresh the macro runtime after a short debounce; the editor never sends output itself.


## Recording

Recording uses process-wide Windows low-level keyboard and mouse hooks only during an active recording session. It records physical key down/up, mouse button down/up, wheel events, and optionally relative mouse movement sampled at approximately 60 Hz. Optional delays preserve elapsed time between captured actions. The recording table updates as each input and delay is captured.

HOTASBridge-generated SendInput events are ignored using injected-event flags and the shared input marker, preventing recursive capture. Auto-repeat keydown messages are collapsed. F12 requests a stop without becoming part of the macro; stopping with the UI removes the trailing click on the Stop button. Compilation is bounded to 1024 actions and ten minutes per recorded delay.

While mapping is running, a HOTAS button press records its replayable mapped Xbox, keyboard, or mouse output. Continuous axis updates are intentionally excluded so a high-rate input cannot flood the macro. An unmapped physical button can be learned as the macro trigger, but it has no output action to add to a recording until it is mapped.

## Import And Export

**Export** writes one selected macro to a versioned `.hotasmacro` JSON package. **Import** validates the 2 MB package limit and supported package schema, assigns new macro/action/group identities, creates a unique name in the active profile, and leaves the imported macro disabled for review.

Packages contain macro configuration only. They never contain a source profile ID, device runtime state, held output state, scheduler state, or diagnostics.

## Interaction References

The Easy workflow follows established device-software conventions without copying another product's data model:

- Razer documents recording or manually inserting keyboard/mouse actions, recorded delays and mouse movement, playback options, and macro import/export.
- Azeron documents single/long/double-press bindings, key combinations, press-once hold/release toggles, cyclic input sequences, repeated macros, mouse clicks, and XInput/DirectInput actions.

Official references: [Razer macro creation](https://mysupport.razer.com/app/answers/detail/a_id/1483), [Razer mouse movement recording](https://mysupport.razer.com/app/answers/detail/a_id/2003), [Razer import/export](https://mysupport.razer.com/app/answers/detail/a_id/1800), [Razer playback assignment](https://mysupport.razer.com/app/answers/detail/a_id/1751/), [Azeron downloads/features](https://www.azeron.eu/downloads), and [Azeron update history](https://www.azeron.eu/update-history/).
## Runtime Behavior

Macros begin only while mapping is active. Values and execution state reset when the profile is configured or mapping restarts. A macro already running does not start a second concurrent instance.

Mapping stop, profile change, emergency reset, suspend, crash recovery, and shutdown cancel active macro work before output neutralization. No runtime state is restored after restart.

## Macro Debugger

The Beta Macro Debugger page displays configured macros, running/waiting state, current action and step, trigger source/count, iteration, last execution, duration, errors, variables, and central scheduler state.

Controls are Pause, Resume, Stop, Restart, and Step One Action. Single-step starts or advances one action and returns to Paused state. A pending delay must be paused or complete before another step can execute.

The page samples immutable snapshots only while visible. It never calls input hardware or output plugins.

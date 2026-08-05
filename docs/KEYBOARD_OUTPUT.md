# Keyboard Output

## Architecture

Keyboard output remains behind `IOutputPlugin` and consumes only standardized `OutputAction` values:

```text
RuntimeSignal -> Mapping Engine -> OutputAction -> Output Manager -> KeyboardOutputPlugin -> SendInput
```

The plugin does not read HOTAS, HID, Raw Input, or profile state. The Mapping Engine owns input interpretation and transform execution. The plugin owns injected-key runtime state, timers, diagnostics, and cleanup.

## Modes

| Mode | Runtime behavior |
| --- | --- |
| Key down/up | Active mapping contributions press the shortcut; inactive contributions release it. |
| Momentary / Hold | Press while processed mapping state is active and release when inactive. |
| Toggle | Mapping runtime state retains the toggle; the plugin receives press/release actions. |
| Pulse | Mapping pulse state produces a bounded press followed by release. |
| Repeat | `StartRepeat` and `StopRepeat` use the central scheduler and mapping-owned interval/pulse settings. |
| Combination | Modifiers and the main key are reference-counted independently and released in reverse order. |
| Analog PWM | `StartPwm` and `StopPwm` use transformed mapping duty/timing metadata. |

Two mappings may share a modifier. Releasing one mapping does not release the shared key until its reference count reaches zero.

## SendInput Safety

`WindowsSendInputKeyboardInjector` supports virtual-key and captured scan-code injection. Every event carries HOTASBridge marker `0x484F5442` in `dwExtraInfo`.

The keyboard capture hook rejects events marked by HOTASBridge, `LLKHF_INJECTED`, or the lower-integrity injected flag. Physical capture events are suppressed while listening so shortcuts such as Alt+F4 do not execute against Windows.

## Keyboard Capture

The Mapping editor supports Xbox and Keyboard output modes. Capture records shortcut text, virtual key, scan code, and Ctrl/Alt/Shift/Windows modifiers. Axis mappings may capture independent primary/positive and negative-direction shortcuts for bipolar PWM.

For an axis input, Easy Mode provides Standard/PWM selection in the left mapping panel. Global PWM reads common timing from the separate **PWM** page; Custom PWM opens a mapping-specific waveform editor by right-clicking the selected visual keyboard key. Full-axis, positive-half, and negative-half modes determine which axis range drives duty. The preview uses red for key-down and green for key-up, becomes a continuous hold at 100% duty, and returns to zero input on double-click. Invalid settings or missing/identical direction keys are reported before the mapping can be applied. Ordinary digital keyboard mappings never expose PWM controls.

The dialog supports single modifier keys and multiple-modifier shortcuts. Clear removes the editor assignment. Duplicate enabled shortcuts in the active profile produce a warning before confirmation and are never silently changed.

Captured values live in the mapping's `OutputConfiguration`. Existing shortcut-only mappings remain compatible and use virtual-key injection.

## Cleanup

Reset, profile change, runtime failure, mapping stop, and shutdown cancel keyboard scheduler jobs, release active targets in reverse order, retry keys whose first release failed, and clear held-key/PWM/repeat state. Runtime key state is never serialized.

## Diagnostics

Diagnostics expose held keys, keys/sec, repeat timers, active PWM outputs, and per-mapping PWM state. PWM stages also publish duty, key state, timer state, scheduler latency, pulse count, and last update time for the Signal Flow Inspector.

Sequences, macros, text output, and multimedia authoring remain future work. Mouse output is now a separate output plugin and never reuses keyboard PWM.

## Visual Keyboard Mapping

The Mapping Editor builds its keyboard from `KeyboardLayoutDefinition` data rather than a screenshot. Current layouts are US ANSI QWERTY and Nordic/Scandinavian ISO. `FollowWindows` selects a layout from the current Windows culture where reliable and falls back to US ANSI.

Each key definition separates:

- stable physical/logical key ID;
- displayed label for the selected visual layout;
- Windows virtual key;
- scan code;
- modifier role.

Mappings persist virtual key, scan code, and modifiers in `outputConfiguration`; they do not rely only on the displayed character. The visual shows unassigned, selected, assigned, duplicate/conflict, active-generated, and selected-modifier states. Hover tooltips list current profile assignments. The capture dialog and searchable key list remain available for keyboard/accessibility workflows.

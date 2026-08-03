# Built-in Transforms

Transform settings are invariant-culture strings stored in each mapping's ordered `transformChain`. Every mapping owns its configuration and runtime state.

## Default Order

```text
Normalization -> Calibration -> Deadzone -> Anti-Deadzone -> Filter
-> Response Curve -> Scaling -> Inversion -> Clamp -> Axis Split
-> Threshold / Toggle / Pulse / Analog PWM / Direction Detection
-> Mapping Behavior -> Output Mapping
```

Schema-v4 migration creates the standard axis chain through Clamp. Schema v8 appends one enabled generated mapping-behavior descriptor after preserved custom transforms. Specialized transforms are added in the Transform Editor and may be reordered.

## Catalog

| Type | Purpose | Common settings |
| --- | --- | --- |
| `normalization` | Convert raw range to bipolar/unipolar | `minimum`, `maximum`, `range` |
| `calibration` | Apply per-control min/center/max/offset | `offset`; profile calibration is preferred |
| `deadzone` | Remove center noise | `mode`, `inner`; radial modes consume optional `pairedAxisValue` |
| `anti-deadzone` | Add minimum movement after center | `amount` |
| `filter` | Smooth noisy values | `mode`, `alpha`, `window` |
| `curve` | Shape response | `type`, `exponent`, `strength`, `points` |
| `scaling` | Apply output range, gain, offset, saturation | `minimum`, `maximum`, `gain`, `offset`, `outer`, `range` |
| `inversion` | Flip bipolar or unipolar axis | `range` |
| `clamp` | Constrain output | `minimum`, `maximum` |
| `axis-split` | Select positive or negative half | `side` |
| `threshold` | Convert value to digital state | `mode`, `lower`, `upper` |
| `toggle` | Retain state after an edge | `trigger` (`OnPress`, `OnRelease`) |
| `pulse` | Produce single/timed/repeating pulse | `mode`, `milliseconds` |
| `analog-pwm` | Produce duty/frequency metadata | `frequencyHz`, `hysteresis`, `fullHoldAtMaximum` |
| `direction-detection` | Detect direction, zones, velocity | `mode`, `threshold`, `lower`, `upper` |
| `behavior` | Apply direct, inverted, release, repeat, and compatibility mapping modes | `behaviorMode`, `invert`, `range`, `repeatMilliseconds`, `pulseMilliseconds` |

## Split Axis To Xbox Triggers

The Mapping Editor Quick Preset **Split axis to Xbox triggers** creates two ordinary mappings from one centered bipolar axis. Select LT or RT on the Xbox visualizer before applying the preset; that selected trigger receives the positive half and the other trigger receives the negative half.

At physical center both trigger outputs are `0`. Positive and negative movement are converted independently from magnitude `0..1`, so neither trigger becomes half-pressed at center. The preset inserts `axis-split` after the generated axis-processing stages and before mapping behavior. Both mappings remain independently editable, disableable, and removable, and no profile schema change is required.


## Behavior Authority

Schema-v8 profiles contain one enabled generated descriptor whose ID ends with -behavior. Toggle and pulse modes use their registered stateful transform types; other mapping modes use the behavior transform. Each publishes the effective behavior and timing into RuntimeSignal metadata for Output Mapping and OutputAction creation.

The legacy behavior, buttonProcessing, pulseDuration, and axisProcessing properties remain migration/authoring projections. They are not authoritative when a behavior descriptor executed. Descriptor-free in-memory compatibility mappings continue to use the legacy stage until a future reviewed schema can remove those fields.

## Curves

Supported curve types are Linear, Exponential, Logarithmic, S-Curve, and Custom Control Points. Custom points use `input:output` pairs separated by semicolons, for example:

```text
-1:-1;-0.5:-0.2;0:0;0.5:0.2;1:1
```

`AxisCurvesView` first selects a device and then one of that device's axes. Its fixed-square preview displays raw input, processed output, live position, deadzone, inversion, sensitivity, and curve shape without stretching when the workspace is resized. The graph receives current unsaved controls as preview values.

Bipolar axes may optionally use independent negative and positive side settings. Each side owns its inner deadzone, outer deadzone, sensitivity, and curve. Side selection is based on the physical value before inversion, so inversion flips the finished response without swapping the settings the user assigned to each physical direction.

Independent sides are disabled by default. Existing profiles continue to use the symmetric `AxisProcessingSettings` fields unchanged. Saving explicitly updates only that axis mapping's generated compatibility descriptors. General transform order and settings are managed in Transform Editor.

## Filters And State

- Moving Average stores the last `window` values; the window is configurable from `2` through `101` and defaults to `5`.
- Exponential Smoothing stores the previous smoothed value and uses `alpha` in `(0, 1]`, defaulting to `0.25`.
- Median Filter stores a configurable `2-101` sample window, orders the samples, and selects the middle value.

State is keyed by mapping ID plus transform ID and is never serialized. Editing a mapping resets only its transform state.

In Transform Editor, select or add a `filter` transform, choose the algorithm, set its alpha or window, and choose **Apply live**. The focused controls write only settings used by the chosen algorithm. Enabled filters are valid only for axis mappings; invalid modes, alpha values, and windows are reported by mapping and profile validation before activation. The runtime resolver remains defensive for legacy profiles and never allows malformed filter settings to terminate signal processing.

## Deadzones

Linear and Square operate on the current scalar control. Circular and Radial calculate vector magnitude when the signal includes `pairedAxisValue`; otherwise they safely use scalar deadzone behavior. Independent X/Y configuration can be expressed by separate mappings or producer metadata until paired-axis grouping is introduced.

## Analog PWM

The Mapping Editor provides guided presets and validated controls for the complete mapping-owned PWM configuration when an axis and Keyboard output are selected.

Analog PWM is valid only on axis-to-keyboard mappings. It applies mapping-owned inversion, smoothing, response curve, duty limits, activation/release hysteresis, full-hold hysteresis, bipolar direction, cycle duration, and minimum phase timing. See `PWM_ENGINE.md`.

The transform emits immutable PWM metadata; Output Manager routes it to the Windows SendInput keyboard plugin and shared scheduler. The Transform Engine does not call Windows APIs.

## Presets

Transform presets contain only:

- preset identity, name, description, timestamps, and tags;
- ordered transform types, enabled states, and settings.

They never contain device IDs, control IDs, mapping IDs, outputs, or runtime state. Presets are stored under `TransformPresets` separately from profiles and support save, load, rename, duplicate, delete, import, and export.

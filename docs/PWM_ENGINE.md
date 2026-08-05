# Analog PWM Engine

## Ownership

| Layer | Responsibility |
| --- | --- |
| Profile mapping | Owns independent transform and output-key configuration. |
| Analog PWM transform | Computes smoothed magnitude, direction, duty, hysteresis, and full-hold state. |
| Mapping Engine | Emits immutable `StartPwm` or `StopPwm` actions. |
| Keyboard plugin | Selects the direction key and owns phase runtime state. |
| Output scheduler | Executes one cooperative phase job per active mapping. |

No thread or task is created per mapping.

## Configuration

| Setting | Default | Rule |
| --- | ---: | --- |
| `axisMode` | FullAxis | Full axis (-1 to +1), positive half (0 to +1), or negative half (0 to -1). |
| `cycleDurationMilliseconds` | 100 | 1 to 60000 ms; Easy Mode exposes 1 to 1000 ms and legacy `frequencyHz` remains supported. |
| `minimumDutyCycle` | 0 | 0 to 1 and not above maximum duty. |
| `maximumDutyCycle` | 1 | 0 to 1. |
| `activationThreshold` | 0.03 | Magnitude required to start PWM. |
| `releaseThreshold` | 0.02 | Must not exceed activation threshold. |
| `fullHoldThreshold` | 0.98 | Enters continuous hold. |
| `fullHoldReleaseThreshold` | 0.96 | Leaves continuous hold. |
| `minimumKeyDownMilliseconds` | 8 | Down plus up minimum cannot exceed cycle duration. |
| `minimumKeyUpMilliseconds` | 8 | Down plus up minimum cannot exceed cycle duration. |
| `responseCurve` | Linear | Linear, Exponential, Logarithmic, or S-Curve. |
| `responseExponent` | 1 | 0.01 to 10. |
| `invert` | false | Reverses bipolar direction or unipolar magnitude. |
| `smoothingAlpha` | 1 | 0.001 to 1; 1 disables smoothing. |
| `hysteresis` | 0.01 | Suppresses insignificant duty changes. |
| `bipolar` | false | Uses independent positive and negative keys. |

Runtime settings belong to each mapping's Analog PWM transform. Direction keys and captured scan codes belong to that mapping's output configuration. Application settings store only the Global PWM authoring defaults; creating or updating a Global PWM mapping copies those values into that mapping.

## Guided Authoring

The Mapping Editor exposes Analog PWM only when all of the following are true:

- the Analog PWM feature is enabled;
- the selected input is an analog axis;
- Keyboard is the selected output.

Easy Mode places a **Keyboard PWM** panel with the selected input controls. Choose **Standard mapping** or **PWM mapping**. PWM mapping then provides:

- **Global PWM**: use the common cycle and minimum key-down/key-up timing from the separate **PWM** navigation page.
- **Custom PWM**: right-click the exact key on the visual keyboard to open its mapping-specific waveform editor.
- **Full axis**: maps -1 to 0% duty, center to 50%, and +1 to 100%.
- **Positive half**: maps center to 0% and +1 to 100%; negative movement is ignored.
- **Negative half**: maps center to 0% and -1 to 100%; positive movement is ignored.

The Global PWM page previews two square-wave cycles on a white timing grid. Red marks effective key-down time and green marks effective key-up time; at 100% duty the red interval fills the cycle and the key remains held. Double-clicking the preview slider returns its input to zero. The page intentionally exposes timing only.

The custom window uses the same preview and keeps only mapping-specific axis range, timing, threshold, full-hold, and minimum-output controls. “Global” describes common authoring defaults, not shared runtime state. Saving still copies the selected values into the mapping so one key can never mutate another key's active configuration.

The editor validates all settings before creating or updating a mapping. Applying PWM writes one normalized `AnalogPwm` transform, preserves unrelated transforms, selects direct mapping behavior, and keeps all key identity in the existing output configuration. Disabling PWM removes only that transform and its PWM-specific negative-key data.

Existing PWM mappings load back into the same panel. Switching the mapping to Xbox or Mouse output removes PWM because those plugins do not consume keyboard duty-cycle actions.

## Timing

The transform performs:

```text
input -> inversion -> smoothing -> magnitude -> response curve -> duty range -> full hold
```

For an active mapping:

```text
duty = minimumDuty + (maximumDuty - minimumDuty) * curvedMagnitude
desiredDown = cycleDuration * duty
down = clamp(desiredDown, minimumDown, cycleDuration - minimumUp)
up = cycleDuration - down
```

Below the release threshold duty is zero. At full-hold threshold duty becomes one until the value drops below the full-hold release threshold. Zero releases the key; full duty cancels the phase timer and holds continuously.

## Bipolar Safety

Negative input selects `pwmNegativeKey`, positive input selects `pwmPositiveKey`, and center releases both. Direction changes release the old target before pressing the new one. Both keys must exist and differ; opposite keys are never held simultaneously by the built-in engine.

## Validation

Mappings are rejected before activation when input is not an axis, output is not Keyboard, a key is missing, timing/duty/threshold/curve values are invalid, minimum phase time exceeds the cycle, or bipolar keys are missing/identical. Invalid mappings remain in the profile but are disabled at runtime and reported by Profile Health.

## Diagnostics

Each mapping reports mapping ID, selected key, duty, PWM state, current key state, active timer, scheduler latency, pulse counter, and last update time. The same snapshot feeds Output Monitor diagnostics and Signal Flow Inspector telemetry.

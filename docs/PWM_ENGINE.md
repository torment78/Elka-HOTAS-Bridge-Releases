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
| `cycleDurationMilliseconds` | 100 | 2 to 60000 ms; legacy `frequencyHz` remains supported. |
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

Settings belong to each mapping's Analog PWM transform. Direction keys and captured scan codes belong to that mapping's output configuration. Nothing is global.

## Guided Authoring

The Mapping Editor exposes Analog PWM only when all of the following are true:

- the Analog PWM feature is enabled;
- the selected input is an analog axis;
- Keyboard is the selected output.

Enable **Analog PWM**, capture the positive-direction key, and optionally enable bipolar mode and capture a distinct negative-direction key. Balanced, Responsive, and Smooth presets provide safe starting points. Every preset copies values into the mapping editor; it is not a shared runtime object.

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

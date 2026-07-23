# Signal-Native Mapping Boundary Completion Report

## Scope

This milestone completed TD-003 by removing the final test-only Mapping Engine compatibility methods. Production behavior was already RuntimeSignal- and OutputAction-native; the regression suite now exercises that same boundary.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Mapping input | RuntimeSignal in production | Preserved |
| Mapping output | OutputAction in production | Preserved |
| Regression path | Mixed `ApplyEvent`, `ApplySignal`, and `ProcessSignal` | `ProcessSignal` only |
| Input acquisition DTO | `InputEvent` inside providers | Preserved as the one-time RuntimeSignal adapter boundary |
| Xbox backend state | `XboxState` | Preserved behind Output Manager/plugins and diagnostics |

## Implementation

- Removed `ApplyEvent` and `ApplySignal` from `IMappingEngine` and `MappingEngine`.
- Migrated axis, trigger, button, toggle, hat, failed-stage, runtime-state, and multi-device simulation tests to `RuntimeSignal -> ProcessSignal -> OutputAction`.
- Kept `XboxOutputActionReducer` as a backend-state helper used by Xbox output behavior tests, not as a Mapping Engine API.
- Updated architecture, RuntimeSignal, Mapping Engine, migration, debt, feature-health, and changelog records.

## Compatibility

- No profile or settings schema change.
- No WPF, input-provider, Mapping Engine behavior, output plugin, scheduler, macro, or scripting behavior change.
- Existing ViGEm Xbox state generation remains intact.

## Verification

- Focused Core mapping/RuntimeSignal tests: 26 passed.
- Focused simulation integration tests: 9 passed.
- Complete automated suite: 289 passed, 0 failed.
- Merged runtime line coverage: 57.72% (`14,254/24,695`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- Release WPF startup smoke: fresh install, existing install with Easy Mode, and Safe Mode passed.

## Remaining Work

The Input project intentionally retains `InputEvent` as a hardware-acquisition DTO before one-time normalization by `RuntimeSignalEngine`. `XboxState` intentionally remains generated output state for the ViGEm backend and diagnostics. Neither is part of Mapping Engine processing.

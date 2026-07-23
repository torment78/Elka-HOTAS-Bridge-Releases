# Chapter 3 Completion Report

Status: Complete with the signal-native output plugin intentionally deferred to the output manager/plugin chapter.

This is the handoff document for Chapter 3: Runtime Signal Model and Processing Engine.

## Requirement Comparison

| Requirement | Classification | Result |
| --- | --- | --- |
| Unified RuntimeSignal | Partially implemented | Expanded and made the active input/runtime object. |
| Required signal fields/types | Partially implemented | Added explicit raw/normalized values, state, quality, and all minimum extensible types. |
| Immutable publication | Missing | Added publication freezing and read-only metadata/history. |
| Every input becomes RuntimeSignal | Partially implemented | Input is now converted once before UI, learning, diagnostics, and mapping. |
| Standardized processing pipeline | Partially implemented | Added ordered signal-only stages and extension interfaces. |
| Runtime state separate from configuration | Partially implemented | Moved mapping state to a dedicated read-only consumer store. |
| Event publication | Missing | Added signal publication with subscriber error isolation. |
| Runtime Signal Cache | Missing | Added engine-owned latest value per active control. |
| Error handling | Partially implemented | Invalid signals and stage/subscriber failures no longer terminate the runtime path. |
| Output plugin signal consumption | Deferred | Output Mapping emits a virtual signal; ViGEm still uses the existing XboxState adapter pending the dedicated output chapter. |

## Implemented Code

- Expanded `RuntimeSignal` with explicit raw, normalized, current, previous, state, quality, required types, metadata, diagnostics, history, and flags.
- Added `IRuntimeSignalEngine` and `RuntimeSignalEngine`.
- Added `IRuntimeSignalCache` and `RuntimeSignalCache` with engine-only mutation.
- Added `IRuntimeSignalStage`, `IRuntimeSignalPipeline`, and `RuntimeSignalPipeline`.
- Added normalization, calibration, deadzone, filtering extension point, curve, scaling, transform, and output-mapping stages.
- Added `IRuntimeMappingStateStore` and `RuntimeMappingStateStore`.
- Updated `MappingEngine` to execute the signal pipeline and adapt its virtual output signals to the existing Xbox state.
- Updated device inspector, curve monitor, learning, diagnostics, and mapping to use one published signal per input.
- Preserved `MappingEngine.ApplyEvent` as a compatibility adapter.

## Runtime Properties

- Cache memory scales with active controls, not event count.
- Pipeline order is deterministic.
- Disabled stages return the input instance.
- Stage telemetry remains UI-independent.
- Invalid values are visible to diagnostics and safely ignored by mapping.
- Subscriber exceptions are isolated.
- Profile configuration does not contain runtime toggle/timer state.

## Tests Added

- Raw/normalized/current/previous field and state/quality coverage.
- Latest-value cache and previous-value behavior.
- Read-only metadata publication.
- Invalid signal recovery.
- Subscriber failure isolation.
- Inserted pipeline-stage ordering.
- Runtime mapping state separation.
- Axis pipeline equivalence with the existing AxisProcessor across multiple values and processing settings.

## Validation

- Debug build: passed, 0 warnings.
- Automated tests: 30 passed.
- Release build: passed, 0 warnings.
- Smoke launch: passed; the app remained alive for the five-second window and was then closed.
- Release output: bundled ViGEmBus installer present; Developer Dashboard files and binary strings absent.

## Deferred Work

- Signal-native `OutputAction` and output plugin consumption.
- Configurable filtering algorithms; the ordered Filtering stage is present but disabled.
- Dedicated runtime scheduler and mapping/output worker.
- Broader typed event bus beyond RuntimeSignal publication.
- Signal Flow Inspector UI.
- Recording/playback and scripting consumers.

## Next Chapter Handoff

Read this report and `docs/RUNTIME_SIGNAL_MODEL.md` before the next chapter. Preserve the signal engine/cache/pipeline contracts and the existing Xbox output compatibility path until an output-specific chapter explicitly replaces it.

## Follow-up: TD-021 Typed Event Bus

The later TD-021 backlog milestone completed the broader publication work deferred by this chapter. `IRuntimeEventBus` now mirrors signal, profile-persistence, runtime-stage diagnostic, plugin-lifecycle, and output-plugin diagnostic messages through one ordered and fault-isolated singleton. The original `IRuntimeSignalEngine.SignalPublished` event remains as a compatibility adapter. See `docs/EVENT_BUS.md` and the TD-021 completion report.
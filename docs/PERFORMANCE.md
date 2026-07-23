# Performance

## Runtime And UI Separation

The input-to-output path is owned by dedicated runtime scheduler lanes and never waits for the WPF dispatcher. HOTASBridge targets sustained processing of a 1 kHz device stream for a direct axis mapping. The integration suite protects this boundary with a 1,000-signal pass-through test that verifies no runtime or output work is rejected.

WPF reads coalesced latest values from the Runtime Signal Cache. Live controls are sampled at the display frame rate, and only the active page receives high-frequency property changes. Output telemetry and mapping-editor keyboard or mouse overlays use lower cadences because they do not participate in output generation.

To avoid periodic UI stalls:

* unchanged Xbox snapshots do not invalidate the controller visualizer;
* hidden inspector, curve, dashboard, and script views are not updated by every UI sample;
* formatted output diagnostics are sampled independently from controller output;
* process-based profile activation does not enumerate processes when no automatic profile target is configured.

## Telemetry

All counters remain UI-independent through `IRuntimeTelemetry`. Chapter 12 exposes:

- Input events/sec and RuntimeSignals/sec
- mapping evaluations/sec and average/worst mapping duration
- transform stage execution duration
- output actions and plugin update rates
- output dispatch duration
- current, average, and worst timed-scheduler latency
- runtime queue depth, peak, rejected count, average wait, and average processing
- diagnostic-log queue depth, peak, and rejected count
- UI FPS and input sample latency
- CPU, working set, managed heap, large object heap, GC collections, and thread count

## Initial Targets

These are engineering targets for profiler comparison, not hardware-independent guarantees.

| Measure | Initial target |
| --- | --- |
| UI sampling | 30 FPS minimum while mapping is active |
| Runtime queue rejection | 0 |
| Runtime queue warning depth | Below 1024 items |
| Runtime signal average wait | Below 5 ms under normal HOTAS input |
| Average mapping evaluation | Below 1 ms for representative profiles |
| Timed output scheduler latency | Below 10 ms under normal load |
| Shutdown drain | Completes without abandoned accepted work |

Hardware validation and recorded sessions determine whether these targets need adjustment. No pooling, thread priority, affinity, or unsafe memory optimization should be added without a measured regression.

## Runtime Performance Profiler

The Debug-only Performance Profiler records telemetry every 250 ms. A versioned `RuntimeTelemetrySession` captures all numeric telemetry and stage execution durations, including rates, queues, latency, CPU, memory, FPS, and GC counters. Capture, averaging, and comparison are implemented by UI-independent Core analysis.

Controls:

- Start Recording
- Stop Recording
- Save Session
- Load Session A
- Compare Session A with Session B

Sessions are atomic JSON files under `Diagnostics/PerformanceSessions`, persisted through `IRuntimeTelemetrySessionStore`. Comparison uses the arithmetic mean of each metric and reports `Session B - Session A`. Storage IDs do not expose arbitrary paths, and invalid, unsupported, locked, or unreadable session files are isolated from valid history. Retention policy and additional export formats remain future extensions of the store boundary.

The profiler XAML, code-behind, view model, navigation entry, and runtime sampling are excluded from Release compilation. A future explicit developer setting may provide a Release opt-in; Chapter 12 does not enable one.

## Allocation Policy

The implementation reduces UI pressure by coalescing latest values and keeps queue snapshots lightweight. RuntimeSignal and OutputAction immutability is preserved. Object pools are deferred until profiler data identifies allocation or GC pressure that justifies their complexity.

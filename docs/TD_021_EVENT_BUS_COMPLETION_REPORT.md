# TD-021 Typed Runtime Event Bus Completion Report

Status: Complete.

## Comparison

| Requirement | Before | Result |
| --- | --- | --- |
| Shared typed publication | Signal-only .NET event | One UI-independent generic bus registered as a process singleton. |
| Signal messages | Legacy event only | Immutable cached signals are mirrored to `RuntimeSignalPublishedMessage`. |
| Profile messages | Direct persistence calls | Written and skipped change-aware saves publish typed results. |
| Diagnostic messages | Pollable snapshots only | Every stored runtime stage is also announced through a typed message. |
| Plugin messages | Catalog polling | Committed lifecycle transitions publish old/new state and optional details. |
| Output messages | Manager polling | Every sampled plugin diagnostic is published independently of telemetry availability. |
| Subscriber safety | Signal event isolation only | Every typed handler is isolated, logged, and counted; later handlers continue. |

## Implementation

- Added `IRuntimeEvent`, `IRuntimeEventBus`, `RuntimeEventBus`, and immutable bus snapshots in Core.
- Preserved synchronous publisher-thread delivery for deterministic order and documented the non-blocking subscriber rule.
- Added exact-type subscriptions with idempotent disposal and invocation outside the registry lock.
- Wired one singleton through App startup and Microsoft DI.
- Preserved all existing compatibility events and state stores.
- Supplied Output Manager with the composition root's shared Plugin Catalog instead of a private duplicate.

## Verification

- Focused Core tests: 233 passed.
- Focused Integration tests: 101 passed.
- Debug solution build: passed with zero warnings and zero errors.
- Full automated suite: 351 passed, 0 failed, 0 skipped.
- Merged runtime coverage: 58.25% (`15,566/26,724`).
- Debug and Release: zero warnings, zero errors.
- Architecture validator: zero errors, warnings, or suggestions; architecture-review gates passed.
- WPF smoke: fresh install, existing Easy/Advanced navigation, and Safe Mode startup passed.
- Release readiness remains false because five existing manual release blockers and eight manual acceptance checks remain open.

## Deferred

- Durable event replay and asynchronous fan-out require separate persistence and backpressure designs.
- External plugin subscriptions remain deferred with third-party loading/trust policy.
- Network transport remains intentionally shelved.
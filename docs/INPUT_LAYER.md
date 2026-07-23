# Input Layer

This document defines the Chapter 4 Input Device Layer for HOTASBridge Version 2.

## Requirement Comparison

| Requirement | Initial State | Chapter 4 Result |
| --- | --- | --- |
| Discover devices | Implemented | Existing HID, Raw Input, virtual HID, and simulation discovery preserved behind providers. |
| Read controls | Implemented | Existing HID background readers and simulation source preserved. |
| Enumerate controls beyond legacy limits | Implemented | HID capability enumeration and simulated devices continue without a 32-button assumption. |
| Common provider interface | Partial | Added signal-native `IInputProvider` for initialization, enumeration, start/stop, signals, errors, lifecycle, status, and disposal. |
| Publish RuntimeSignals | Partial | `InputProviderAdapter` converts legacy `InputEvent` values at the provider boundary; the app receives only RuntimeSignals. |
| Automatic hot-plug/removal | Missing | Native Windows HID notifications wake `CompositeInputProvider`; it publishes connected/disconnected/reconnected events and retains safety/degraded polling. |
| Device health | Missing | Added Connected, Active, Idle, Error, Unsupported, Disabled, and Disconnected states. |
| Stable identity strategy | Partial | Added GUID/container identity fields and explicit matching priority while preserving existing stable IDs/profile compatibility. Windows HID now retrieves validated SetupAPI Container IDs and reconciliation backfills profiles additively. |
| Multiple simultaneous devices | Implemented | Existing profile groups and mapping behavior preserved. |
| Input Learn Mode | Partial | Added selected-device/group scope, baseline noise rejection, first-input capture, highlighting, details, confirm, retry, and capture without Xbox output. |
| DirectInput provider | Missing | Deferred; there is no current implementation or approved dependency. The provider contract can accept it without app changes. |

## Provider Architecture

```mermaid
flowchart LR
    subgraph Windows["Windows Input APIs"]
        HIDAPI["SetupAPI and HID APIs"]
        RawAPI["Raw Input discovery"]
        NotifyAPI["RegisterDeviceNotification / WM_DEVICECHANGE"]
    end

    HIDCatalog["WindowsHidDeviceProvider"]
    HIDReader["WindowsHidInputService"]
    RawCatalog["WindowsRawInputDeviceProvider"]
    Notifications["WindowsInputDeviceNotificationSource"]
    Simulation["SimulatedInputService"]

    HIDAdapter["IInputProvider: Windows HID"]
    RawAdapter["IInputProvider: Raw Input"]
    SimAdapter["IInputProvider: Simulation"]
    Manager["CompositeInputProvider"]
    Devices["IDeviceCoordinator"]
    Monitoring["IInputMonitoringCoordinator"]
    SignalEngine["RuntimeSignalEngine"]
    Consumers["UI, Mapping, Diagnostics, Learn Mode"]

    HIDAPI --> HIDCatalog
    HIDAPI --> HIDReader
    RawAPI --> RawCatalog
    HIDCatalog --> HIDAdapter
    HIDReader --> HIDAdapter
    RawCatalog --> RawAdapter
    Notifications --> Manager
    Simulation --> SimAdapter
    HIDAdapter --> SignalEngine
    SimAdapter --> SignalEngine
    HIDAdapter --> Manager
    RawAdapter --> Manager
    SimAdapter --> Manager
    SignalEngine --> Manager
    Manager --> Devices
    Devices --> Consumers
    Manager --> Monitoring
    Monitoring --> Consumers
```

The composition root is the only application code that constructs concrete providers. `MainViewModel` depends on `IInputProvider` for provider events and cached signals, on `IDeviceCoordinator` for discovery/profile membership, and on `IInputMonitoringCoordinator` for selected-device monitoring lifecycle.

`DeviceCoordinator` serializes discovery, applies demo-device visibility, reconciles reconnect identities, and owns stable-ID profile membership mutations. It never constructs WPF view models or starts outputs.

`WindowsInputDeviceNotificationSource` owns the Win32 message-only window and HID interface registration inside the Input project. `CompositeInputProvider` coalesces native bursts for 150 ms, then runs the same serialized provider enumeration and lifecycle comparison used by manual refresh. Native mode retains a 30-second safety poll; failed or unavailable registration automatically uses the existing two-second poll.

`InputMonitoringCoordinator` owns input-only start/stop, cancellation, equivalent-selection deduplication, and watchdog restart. It does not start output plugins, so device inspection, Learn Mode, mapping preparation, and curve editing remain available while generated output is stopped.

## Provider Contract

Every provider exposes:

- stable provider ID and display name;
- declared discovery/live-input/control/source capabilities;
- current provider status;
- read-only Runtime Signal Cache;
- signal, device lifecycle, and provider error events;
- initialize, enumerate, start, stop, and asynchronous disposal operations.

`InputProviderAdapter` preserves the provider-specific `IInputDeviceCatalog` and `IInputEventSource` implementations internally. Compatibility `InputEvent` objects do not leave the adapter. The unused pre-provider `CompositeInputCatalog` and `CompositeInputEventSource` aggregates were retired; `CompositeInputProvider` is the only application-facing aggregate.

## Current Providers

| Provider | Discovery | Live Input | Control Enumeration | Notes |
| --- | --- | --- | --- | --- |
| Windows HID | Yes | Yes | Yes | Primary physical/virtual HOTAS provider using the existing native implementation. |
| Raw Input | Yes | No | Generic discovery controls | Discovery-only corroboration; duplicate paths are merged in favor of HID. |
| Simulation | Yes | Yes | Yes | Development/demo physical and virtual devices. Hidden unless demo devices are enabled. |
| Virtual HID devices | Yes | Yes | Yes | vJoy and similar installed devices flow through Windows HID and are classified virtual. |
| DirectInput | Deferred | Deferred | Deferred | Provider slot exists; no current backend/library is present. |

Future Bluetooth, network, serial, CAN, Arduino, and replay providers implement the same interface.

## Signal Publication

```mermaid
sequenceDiagram
    participant Device as Input Device
    participant Reader as Existing Input Reader
    participant Adapter as InputProviderAdapter
    participant Engine as RuntimeSignalEngine
    participant Manager as CompositeInputProvider
    participant Consumer as Runtime Consumer

    Device->>Reader: hardware report
    Reader->>Adapter: compatibility InputEvent
    Adapter->>Engine: publish and normalize
    Engine-->>Adapter: immutable RuntimeSignal
    Adapter->>Manager: SignalReceived
    Manager->>Consumer: RuntimeSignal
```

Providers never run mappings or generate output. Minimal validation, timestamps, range normalization, quality flags, and diagnostics occur before publication. Deadzones, curves, filtering, and scaling remain Transform Engine responsibilities.

Windows HID discovery and live report processing share `HidValueParsing`. The shared boundary preserves the native 72-byte `HIDP_VALUE_CAPS` layout, limits malformed or oversized usage ranges to at most 64 controls, sign-extends 8/12/16/32-bit values from the report bit width, clamps axis values to declared logical ranges, and leaves hat null values unclamped for `HatNormalizer`. Bipolar axes scale negative and positive sides independently so both declared endpoints normalize exactly.

## Device Health

The composite manager derives health from discovery, provider status, and recent signals:

- Connected: discovered but no live sample has been observed.
- Active: a sample was observed within two seconds.
- Idle: samples exist but the latest is older than two seconds.
- Error: the owning provider reports an error.
- Disconnected: emitted with removal lifecycle events.
- Unsupported and Disabled: available for provider/configuration decisions.

Provider and device counts, active/idle counts, enumerated controls, and provider statuses are published through `IRuntimeTelemetry`.

## Input Learn Mode

Learn Mode uses `InputLearnSession`:

1. Scope to the selected Mapping Editor device, or the selected profile group if no single device is selected.
2. Snapshot current cached values as the baseline.
3. Ignore analog movement below the configurable 0.18 threshold.
4. Ignore signals outside the scope and invalid signals.
5. Stop on the first meaningful input.
6. Highlight the device/control in Mapping Editor and Device Inspector.
7. Display device, control, ID, raw value, and normalized value.
8. Confirm to create the mapping, Retry to establish a new baseline, or Cancel.

If mapping is stopped, Learn Mode starts only the selected input providers. It does not connect or update the Xbox output.

## Error And Resource Rules

- Provider failures are logged and published as structured error events.
- A failing provider does not prevent other providers from enumerating or reading.
- HID readers retain their existing cancellation and handle-disposal behavior.
- Provider shutdown occurs in reverse order.
- Monitoring and input capture use cancellation tokens and no UI-thread blocking calls.
- The native notification window runs on one background message thread and is stopped before provider disposal.
- The app keeps the existing ViGEm output path completely outside this layer.

## Deferred Work

- DirectInput backend selection and compatibility testing.
- Native suspend/resume events; the event contract already represents them.

## Easy Input and Hat Milestone

The Windows HID provider now attaches a `HatDescriptor` to every enumerated hat. The descriptor carries provider ID, encoding, logical range, null state, direction count, and optional center-button capability. Live reports produce a `HatState` containing the preserved raw value and one normalized direction. Repeated identical directions are not republished; one centered event is published when the control returns to idle.

Supported normalization contracts are:

- DirectInput hundredths (`-1`/unsigned max centered, 0 through 31500 directions);
- HID zero-based directions with a provider-declared null;
- HID one-based directions with zero center;
- individual direction buttons, including combined diagonals;
- simulated zero-based hats;
- provider-specific metadata through the extensible descriptor.

A provider-less legacy conversion remains only for old tests/compatibility. New live providers must describe their encoding rather than rely on one global numeric interpretation.

### Provider Correlation

`CompositeInputProvider` groups only strong same-interface representations. It prefers a provider that supplies both live input and control enumeration, exposes a user-facing warning listing correlated providers, logs/telemeters the correlation once, and keeps the preferred identity stable. Distinct processed virtual paths such as vJoy are not merged into a physical controller.

The `AllowDuplicateInputProviders` Advanced setting includes correlated representations with distinct stable IDs after restart. This is diagnostic control, not the normal path.

### Monitoring Without Outputs

Physical provider initialization and Runtime Signal Cache updates do not depend on mapping/output activation. Device Inspector, Learn Input, and curve testing can therefore run while the virtual Xbox controller and SendInput outputs remain stopped. Start Mapping activates the mapping/output lifecycle only.

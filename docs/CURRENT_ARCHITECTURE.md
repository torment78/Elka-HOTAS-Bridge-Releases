# Current Architecture

This document describes the implementation that exists now. It intentionally documents reality, not the desired Version 2 end state.

## Repository Layout

```text
HOTASBridge/
  HOTASBridge.sln
  Directory.Build.props
  global.json
  src/
    HOTASBridge.App/
    HOTASBridge.Core/
    HOTASBridge.Input/
    HOTASBridge.Output/
    HOTASBridge.Infrastructure/
  tests/
    HOTASBridge.Core.Tests/
    HOTASBridge.IntegrationTests/
  docs/
  installer/
  scripts/
  third_party/
    ViGEmBus/
```

## Project Relationships

```text
HOTASBridge.App
  -> HOTASBridge.Core
  -> HOTASBridge.Input
  -> HOTASBridge.Output
  -> HOTASBridge.Infrastructure

HOTASBridge.Input -> HOTASBridge.Core
HOTASBridge.Output -> HOTASBridge.Core
HOTASBridge.Infrastructure -> HOTASBridge.Core

HOTASBridge.Core.Tests -> HOTASBridge.Core
HOTASBridge.IntegrationTests -> Core/Input/Output/Infrastructure
```

## Runtime Flow

```text
WPF App startup
  -> create AppPaths and JsonFileLog
  -> assess deployment prerequisites without blocking startup
  -> manually construct input providers, profile store, output and deployment services, MainViewModel
  -> MainViewModel loads profiles and refreshes devices
  -> evaluate First Run Wizard policy after the shell is ready

Input discovery
  -> WindowsHidDeviceProvider
  -> WindowsRawInputDeviceProvider
  -> SimulatedInputService
  -> InputProviderAdapter instances
  -> WindowsInputDeviceNotificationSource topology wakeups
  -> CompositeInputProvider deduplication, lifecycle monitoring, and polling fallback

Mapping runtime
  -> CompositeInputProvider starts selected HID/simulation providers
  -> provider adapters publish RuntimeSignals to MainViewModel
  -> MainViewModel dispatches to WPF Dispatcher
  -> Device Inspector, curve views, recent event log update
  -> MappingEngine applies matching profile mappings
  -> XboxState is submitted to IVirtualGamepadOutput

Output runtime
  -> VirtualXboxOutputService checks ViGEmBus
  -> creates ViGEm Xbox 360 controller when available
  -> submits XboxState reports
  -> neutralizes and disconnects on stop/shutdown
```

## Major Services

| Service/Class | Project | Responsibility | Current Classification |
| --- | --- | --- | --- |
| `MainViewModel` | App | UI state, profile interaction, device refresh, mapping loop coordination | Candidate for Refactoring |
| `ViGEmBusDriverService` | Output | Shared driver detection and explicitly confirmed elevated installer launch | Stable Adapter |
| `DeploymentAssessmentService` | Infrastructure | Prerequisite and install-scope assessment independent of WPF | Stable Foundation |
| `DeploymentBackupService` | Infrastructure | Versioned deployment ZIP backup and path-safe restore | Stable Foundation |
| `TrayIconService` | App | System tray integration | Candidate for Extension |
| `CompositeInputProvider` | Input | App-facing provider aggregation, deduplication, native-notification/fallback lifecycle, health, errors, and signals | Stable Foundation |
| `WindowsInputDeviceNotificationSource` | Input | Private Win32 message-only window and HID topology notifications | Stable Adapter |
| `WindowsHidDeviceProvider` | Input | Enumerates HID joystick/gamepad-class devices, controls, and SetupAPI Container IDs | Stable Foundation |
| `WindowsHidInputService` | Input | Reads selected HID reports and emits input events | Candidate for Extension |
| `WindowsRawInputDeviceProvider` | Input | Raw Input discovery foundation | Candidate for Extension |
| `SimulatedInputService` | Input | Simulated devices and live events for demo/testing | Stable |
| `DuplicateInputDetector` | Core | Warns about likely duplicate physical/virtual paths | Stable |
| `MappingEngine` | Core | Converts matching input events into generated Xbox state | Candidate for Extension |
| `AxisProcessor` | Core | Normalization, deadzone, curve, stick/trigger conversion | Stable |
| `CurveProcessor` | Core | Response curve processing | Stable |
| `DeviceIdentityMatcher` | Core | Profile device reconnection scoring | Stable |
| `VirtualXboxOutputService` | Output | ViGEm-backed Xbox 360 output with monitor fallback | Stable Adapter |
| `OutputManager` | Output | Consumes OutputActions, maintains Xbox state, routes backends, and publishes diagnostics | Stable Foundation |
| `JsonProfileStore` | Infrastructure | Versioned, validated, atomic profile persistence and migration backup | Stable |
| `JsonApplicationSettingsStore` | Infrastructure | Auto Save and recent-profile settings persistence | Stable Foundation |
| `OfflineUpdateService` | Infrastructure | Confirmation/signature-aware update boundary with no network side effects | Stable Foundation |
| `ProfileValidator` | Core | UI-independent profile validation and health reports | Stable Foundation |
| `JsonFileLog` | Infrastructure | Bounded batched JSON-lines logging, deterministic flush, retention, and telemetry | Stable Foundation |

## Current Interfaces

| Interface | Responsibility |
| --- | --- |
| `IInputProvider` | App-facing initialization, discovery, signal, lifecycle, health/error, start/stop, and disposal boundary. |
| `IInputDeviceCatalog` | Compatibility catalog used inside provider adapters. |
| `IInputEventSource` | Compatibility event source used inside provider adapters. |
| `IProfileStore` | Loads, validates, saves, imports, exports, duplicates, renames, and deletes profiles. |
| `IApplicationSettingsStore` | Loads and saves application settings independently from profiles. |
| `IVirtualGamepadDriverService` | Reports driver/package health and launches installation only after explicit confirmation. |
| `IDeploymentAssessmentService` | Produces prerequisite and install-scope reports. |
| `IDeploymentBackupService` | Creates and restores versioned deployment archives. |
| `IUpdateService` | Exposes update checks/install policy independently from UI and transport. |
| `IMappingEngine` | Indexes mappings, processes RuntimeSignals, exposes runtime status, and emits OutputActions. |
| `IOutputManager` | Consumes OutputActions and routes supported batches to output backends. |
| `IStructuredLog` | Emits structured application events. |
| `IVirtualGamepadOutput` | Connects, updates, neutralizes, and disconnects virtual output. |

## External Dependencies

| Dependency | Location | Purpose |
| --- | --- | --- |
| .NET SDK 10.0.301 | `global.json` | Build/runtime target. |
| WPF | `HOTASBridge.App` | Windows desktop UI. |
| Windows Forms | `HOTASBridge.App` | Tray icon support. |
| Windows HID API (`hid.dll`) | `HOTASBridge.Input` | HID discovery/report parsing. |
| SetupAPI (`setupapi.dll`) | `HOTASBridge.Input` | Device-interface enumeration. |
| Kernel32 (`kernel32.dll`) | `HOTASBridge.Input` | HID device file handles/report reads. |
| `Nefarius.ViGEm.Client` 1.21.256 | `HOTASBridge.Output` | Virtual Xbox 360 controller client. |
| ViGEmBus 1.22.0 setup EXE | `third_party/ViGEmBus` | Bundled driver installer for virtual Xbox bus. |
| MSTest | test projects | Automated tests. |

## Configuration and Storage

| Area | Current Behavior |
| --- | --- |
| Profiles | Schema v9 JSON files in `%LOCALAPPDATA%\HOTASBridge\Profiles`, with optional persisted mapping graphs. |
| Application settings | Schema v6 JSON at `%LOCALAPPDATA%\HOTASBridge\settings.json`. |
| Migration backups | Exact pre-migration JSON in `%LOCALAPPDATA%\HOTASBridge\Backups`. |
| Templates | Built-in catalog; external template path reserved separately. |
| Diagnostics/temp | Separate `%LOCALAPPDATA%\HOTASBridge\Diagnostics` and `Temp` directories. |
| Logs | Daily JSON lines in `%LOCALAPPDATA%\HOTASBridge\Logs`, batched asynchronously with configurable 1-365 day retention. |
| Build settings | Nullable enabled, latest language/analyzers, warnings as errors, deterministic builds. |
| Driver package | Copied to app output under `Drivers\ViGEmBus`. |

## Threading Model

| Area | Current Behavior |
| --- | --- |
| WPF UI | Main thread/Dispatcher. |
| HID report reading | Background tasks per selected HID device. |
| Mapping | Currently performed inside Dispatcher work scheduled by input event handling. |
| Output submission | Fire-and-forget `UpdateAsync` from UI-dispatched handler. |
| Logging | Bounded multi-writer channel feeding one batched asynchronous JSON-lines writer with explicit flush barriers. |
| Driver installation | Runs only after explicit wizard confirmation; the elevated process is awaited asynchronously. |

## Known Strengths

- Clear project separation exists already.
- Core processing is free of WPF dependencies.
- Output-driver-specific code is isolated in `HOTASBridge.Output`.
- Profiles are human-readable schema v9 JSON with validation, optional branching graphs, atomic writes, backup-preserving migration/replacement, compatibility preview, and package-provider boundaries.
- Runtime mapping and output state remain separate from persisted configuration.
- HID parsing now uses HID logical ranges instead of ad hoc axis assumptions.
- Provider-described hats and D-pads normalize into RuntimeSignals; Xbox, keyboard, and mouse outputs share the Output Manager and scheduler boundaries.
- Experimental Lua scripting is limited to local or signature-trusted code, explicit host permissions, bounded resources, and a fail-closed isolation policy; untrusted packages do not execute.
- Build is clean with warnings as errors.
- Automated tests protect core mapping, axis/curve processing, profile serialization, simulation, and output neutralization.

## Known Limitations and Technical Debt

- Microsoft DI now registers and validates the application service graph in `ApplicationComposition.cs`; further coordinator extraction can move additional lifecycle ownership out of `MainViewModel` incrementally.
- `MainViewModel` coordinates too many responsibilities and owns mapping loop orchestration.
- RuntimeSignal publication, indexed mapping, runtime contributions, OutputAction batches, and Xbox Output Manager dispatch are active.
- Central runtime and output schedulers are active; `MainViewModel` still coordinates the UI-facing runtime lifecycle and throttled presentation updates.
- Diagnostics now have a first-class telemetry foundation; exporter/profiler workflows are still future work.
- UI pages are implemented in one large `MainWindow.xaml`.
- Hardware-specific validation remains manual.
- Release engineering supports certificate-store signing, signed setup/uninstaller generation, SHA-256 manifests, independent verification, and disposable-machine acceptance; no production certificate or clean-machine evidence has been recorded yet.
- Output backend is isolated but not yet plugin-hosted.

## Source File Classification

| Source File | Category | Notes |
| --- | --- | --- |
| `src/HOTASBridge.App/App.xaml.cs` | Stable lifecycle shell | Owns WPF startup/recovery order, watchdog activation, exception boundaries, and coordinated shutdown. |
| `src/HOTASBridge.App/ApplicationComposition.cs` | Stable composition root | Registers the application service graph with Microsoft DI and preserves explicit runtime startup dependencies. |
| `src/HOTASBridge.App/App.xaml` | Stable | Minimal WPF app resource root. |
| `src/HOTASBridge.App/AssemblyInfo.cs` | Stable | WPF theme metadata. |
| `src/HOTASBridge.App/GlobalUsings.cs` | Stable | Shared app usings. |
| `src/HOTASBridge.App/MainWindow.xaml` | Candidate for Refactoring | Large single-file shell; keep working UI before modularizing. |
| `src/HOTASBridge.App/MainWindow.xaml.cs` | Stable | Minimal code-behind. |
| `src/HOTASBridge.App/Controls/CurveEditorControl.cs` | Candidate for Extension | Functional rendering; editable custom points deferred. |
| `src/HOTASBridge.App/Controls/StickVisualizer.cs` | Stable | Small visual control. |
| `src/HOTASBridge.App/Controls/XboxVisualizer.cs` | Candidate for Extension | Visual mapping/output control; could grow selection/tooltip behavior. |
| `src/HOTASBridge.App/Converters/BoolToBrushConverter.cs` | Stable | Simple UI converter. |
| `src/HOTASBridge.App/Views/FirstRunWizardWindow.xaml` | Stable Foundation | Skippable nine-step setup UI over service-owned runtime behavior. |
| `src/HOTASBridge.App/ViewModels/MainViewModel.Deployment.cs` | Candidate for Refactoring | Coordinates setup, backup, and update UI commands over deployment interfaces. |
| `src/HOTASBridge.App/Services/TrayIconService.cs` | Candidate for Extension | Tray behavior exists; needs wider lifecycle validation. |
| `src/HOTASBridge.App/ViewModels/DeveloperDashboardViewModel.cs` | Candidate for Extension | Debug-only telemetry consumer; now reads `IRuntimeTelemetry` snapshots. |
| `src/HOTASBridge.App/ViewModels/DeviceViewModels.cs` | Candidate for Extension | Device/inspector models work; may split as UI grows. |
| `src/HOTASBridge.App/ViewModels/MainViewModel.cs` | Candidate for Refactoring | Central orchestration class; future runtime/scheduler separation needed. |
| `src/HOTASBridge.App/ViewModels/MappingAndXboxViewModels.cs` | Candidate for Extension | Small view models; developer metrics may extend. |
| `src/HOTASBridge.App/ViewModels/NavigationItem.cs` | Stable | Simple navigation record. |
| `src/HOTASBridge.App/ViewModels/ObservableObject.cs` | Stable | MVVM base. |
| `src/HOTASBridge.App/ViewModels/RelayCommand.cs` | Stable | Command helpers. |
| `src/HOTASBridge.Core/Abstractions/Services.cs` | Stable Foundation | Mapping and Output Manager action contracts; dynamic plugin interfaces remain future work. |
| `src/HOTASBridge.Core/Domain/AxisProcessingSettings.cs` | Candidate for Extension | Axis pipeline settings foundation. |
| `src/HOTASBridge.Core/Domain/DeviceIdentity.cs` | Stable | Stable ID creation. |
| `src/HOTASBridge.Core/Domain/Enums.cs` | Candidate for Extension | Current enums are functional; output/action model will expand. |
| `src/HOTASBridge.Core/Domain/InputModels.cs` | Stable Foundation | `InputEvent` remains compatibility input and adapts into `RuntimeSignal`. |
| `src/HOTASBridge.Core/Domain/ProfileMigration.cs` | Stable Foundation | v1-to-v2 migration, compatibility synchronization, and extension-node preservation. |
| `src/HOTASBridge.Core/Domain/Profiles.cs` | Stable Foundation | Schema v3 mapping metadata, conditions, priority, transforms, and generic output configuration. |
| `src/HOTASBridge.Core/Domain/XboxState.cs` | Stable | Focused output state model. |
| `src/HOTASBridge.Core/Runtime/RuntimeSignal.cs` | Stable Foundation | Unified signal model for normalized runtime processing. |
| `src/HOTASBridge.Core/Runtime/RuntimeSignalEngine.cs` | Stable Foundation | Owns immutable input publication, cache updates, error isolation, and signal telemetry. |
| `src/HOTASBridge.Core/Runtime/RuntimeSignalCache.cs` | Stable Foundation | Read-only latest signal per active control; engine-only mutation. |
| `src/HOTASBridge.Core/Runtime/RuntimeSignalPipeline.cs` | Stable Foundation | Ordered insertable RuntimeSignal stage contract and diagnostics. |
| `src/HOTASBridge.Core/Runtime/RuntimeSignalStages.cs` | Candidate for Extension | Standard signal stages; configured transforms execute through the active descriptor engine. |
| `src/HOTASBridge.Core/Runtime/RuntimeMappingState.cs` | Stable Foundation | Runtime-only mapping values, timestamps, toggles, pulses, and timer state. |
| `src/HOTASBridge.Core/Runtime/RuntimeDiagnostics.cs` | Stable Foundation | Stage diagnostic metadata consumed by the Signal Flow Inspector. |
| `src/HOTASBridge.Core/Runtime/RuntimeTelemetry.cs` | Stable Foundation | UI-independent telemetry contracts and snapshots. |
| `src/HOTASBridge.Core/Processing/AxisProcessor.cs` | Stable | Protected by tests. |
| `src/HOTASBridge.Core/Processing/DuplicateInputDetector.cs` | Stable | Active provider-independent duplicate warning logic. |
| `src/HOTASBridge.Core/Processing/CurveProcessor.cs` | Stable | Protected by tests. |
| `src/HOTASBridge.Core/Processing/DeviceIdentityMatcher.cs` | Stable | Protected by tests. |
| `src/HOTASBridge.Core/Processing/MappingEngine.cs` | Stable Foundation | Indexed RuntimeSignal evaluation, conditions, contributions, conflicts, and OutputAction generation. |


| `src/HOTASBridge.Input/CompositeInputProvider.cs` | Stable Foundation | Active app-facing provider manager and lifecycle monitor. |
| `src/HOTASBridge.Input/InputProviderAdapter.cs` | Stable Foundation | Wraps existing catalogs/readers and publishes RuntimeSignals. |
| `src/HOTASBridge.Input/DuplicateInputDetector.cs` | Compatibility | Obsolete wrapper over the Core implementation. |
| `src/HOTASBridge.Input/SimulatedInputService.cs` | Stable | Test/demo foundation. |
| `src/HOTASBridge.Input/WindowsHidDeviceProvider.cs` | Stable Foundation | SetupAPI/HID discovery, control parsing, and Container ID property access have focused native-boundary regressions. |
| `src/HOTASBridge.Input/WindowsHidInputService.cs` | Candidate for Extension | Important native live input; needs hardware validation and diagnostics. |
| `src/HOTASBridge.Input/WindowsRawInputDeviceProvider.cs` | Candidate for Extension | Discovery foundation. |
| `src/HOTASBridge.Input/WindowsInputDeviceNotificationSource.cs` | Stable Adapter | Tested native HID topology notifications with polling fallback owned by the composite manager. |
| `src/HOTASBridge.Infrastructure/AppPaths.cs` | Stable | Central storage paths. |
| `src/HOTASBridge.Infrastructure/InMemoryRuntimeTelemetry.cs` | Stable Foundation | Thread-safe runtime metrics and stage diagnostic snapshot service. |
| `src/HOTASBridge.Infrastructure/JsonFileLog.cs` | Stable Foundation | Bounded batching, deterministic flush/shutdown, configurable owned-file retention, and telemetry. |
| `src/HOTASBridge.Infrastructure/JsonProfileStore.cs` | Stable | Atomic persistence, validation, workflows, and migration backup protected by tests. |
| `src/HOTASBridge.Infrastructure/JsonApplicationSettingsStore.cs` | Stable Foundation | Atomic Auto Save/recent-profile settings persistence. |
| `tests/HOTASBridge.Core.Tests/ProfileSystemTests.cs` | Stable | Schema migration, health, and template regression protection. |
| `tests/HOTASBridge.IntegrationTests/ProfilePersistenceTests.cs` | Stable | Persistence, backup, workflow, and settings regression protection. |
| `src/HOTASBridge.Output/VirtualXboxOutputService.cs` | Candidate for Extension | Working ViGEm backend; plugin manager later. |
| `tests/HOTASBridge.Core.Tests/AxisProcessorTests.cs` | Stable | Regression protection. |
| `tests/HOTASBridge.Core.Tests/CurveProcessorTests.cs` | Stable | Regression protection. |
| `tests/HOTASBridge.Core.Tests/DeviceAndProfileTests.cs` | Stable | Regression protection. |
| `tests/HOTASBridge.Core.Tests/MappingEngineTests.cs` | Stable | Regression protection. |
| `tests/HOTASBridge.Core.Tests/RuntimeSignalTests.cs` | Stable | RuntimeSignal normalization regression protection. |
| `tests/HOTASBridge.Core.Tests/MSTestSettings.cs` | Stable | Test settings. |
| `tests/HOTASBridge.IntegrationTests/MSTestSettings.cs` | Stable | Test settings. |
| `tests/HOTASBridge.IntegrationTests/SimulationIntegrationTests.cs` | Stable | Integration regression protection. |
| `tests/HOTASBridge.IntegrationTests/TelemetryTests.cs` | Stable | Telemetry snapshot and stage diagnostic regression protection. |

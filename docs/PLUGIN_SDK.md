# Plugin SDK

## Chapter 15 Assessment

| Requirement | Baseline | Chapter 15 result |
| --- | --- | --- |
| Stable plugin identity and metadata | Missing | Versioned `PluginManifest` shared by internal and future external plugins |
| Plugin categories | Separate subsystem contracts | Shared category vocabulary for outputs, inputs, transforms, diagnostics, import/export, profile tools, and UI extensions |
| Discovery | Constructor registration only | Startup JSON manifest discovery under the dedicated Plugins directory |
| Compatibility and API negotiation | Missing | Host API range, manifest schema, semantic version, dependency, duplicate, and cycle validation |
| Invalid-plugin isolation | Runtime output failures only | Invalid/incompatible manifests are disabled while compatible plugins continue |
| Internal plugins use SDK model | Partial | Xbox and Keyboard publish the same manifest used by external candidates |
| External binary loading | Missing | Deferred intentionally; Version 2 discovers metadata but does not execute external assemblies |
| Plugin Manager repository support | Missing | UI-independent catalog, search, repository, package-operation, release-note/rating, and signature extension points |
| Online service | Missing | Deferred by specification; default services are explicitly offline/read-only |

## Boundaries

The SDK is owned by `HOTASBridge.Core.Plugins`. It has no WPF, HID, ViGEm, SendInput, file-system, or network dependency.

Category-specific runtime contracts remain narrow. For example, output plugins implement `IOutputPlugin`, consume only `OutputAction` batches, and receive only the existing scheduler/telemetry/log context. A manifest does not grant access to profiles, devices, mapping state, or UI objects.

Infrastructure owns local manifest I/O. `JsonPluginManifestDiscovery` reads `%LOCALAPPDATA%\HOTASBridge\Plugins\**\plugin.json`, applies a 1 MB limit, and returns candidates or isolated discovery issues. It never calls `Assembly.Load`, constructs plugin types, or contacts a remote service.

## Categories

- Output
- Input Provider
- Transform
- Diagnostics
- Importer
- Exporter
- Profile Tool
- UI Extension

A manifest may declare more than one category. Each executable category will still require a dedicated, published runtime interface before external loading is enabled.

## Compatibility

The Version 2 SDK API is `1.0`. Manifests declare an inclusive minimum and exclusive maximum API version. The catalog also validates:

- manifest schema and stable lowercase ID;
- semantic plugin version;
- category declarations;
- required and optional dependencies;
- dependency version ranges and cycles;
- duplicate IDs, with Internal then Bundled then External precedence;
- safe relative external entry assembly and entry type;
- complete signature metadata when present.

Validation never throws for a bad plugin document. The candidate becomes `Invalid` or `Incompatible`, its lifecycle becomes `Disabled`, and the issue remains available through `PluginCatalogSnapshot`.

## Local Layout

```text
%LOCALAPPDATA%\HOTASBridge\Plugins\
  Vendor.Plugin\
    plugin.json
    Vendor.Plugin.dll        # metadata only in Version 2
```

The application creates the Plugins directory at startup. Dropping a DLL there does not cause code execution.

## Plugin Manager Foundation

`PluginManagerService` consumes `IPluginCatalog`, `IPluginRepository`, and `IPluginPackageService`. It can search installed descriptors and perform compatibility checks today. `OfflinePluginRepository` and `ReadOnlyPluginPackageService` return explicit unavailable results for browse/install/update/uninstall.

Future online support can replace those interfaces without changing the catalog, output runtime, profile schema, or UI view models. Repository items already carry category, latest version, release notes, rating fields, API range, and signature status.

## Best Practices

1. Keep IDs stable and lowercase; never derive profile routing from display names.
2. Declare the smallest API range actually tested.
3. Depend only on published SDK interfaces.
4. Keep initialize/start/stop/reset/dispose idempotent where practical.
5. Never retain or mutate RuntimeSignals or OutputActions owned by another subsystem.
6. Report bounded diagnostics and release every owned output during reset.
7. Treat cancellation as shutdown/control flow, not a plugin fault.
8. Add lifecycle, compatibility, reset, and failure-isolation tests.

See `PLUGIN_API.md`, `PLUGIN_LIFECYCLE.md`, `PLUGIN_MANAGER.md`, and `schema/hotas-plugin-manifest-v1.schema.json`.

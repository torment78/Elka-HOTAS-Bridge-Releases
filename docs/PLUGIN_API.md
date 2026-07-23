# Plugin API

## Public Surface

| API | Purpose |
| --- | --- |
| `PluginManifest` | Stable identity, version, author, description, API range, categories, dependencies, entry point, release notes, and optional signature metadata |
| `IHotasBridgePlugin` | Shared identity/manifest contract for executable plugin categories |
| `IOutputPlugin` | Existing output lifecycle and action-consumer contract |
| `IPluginDiscoverySource` | Returns manifest candidates and isolated discovery issues |
| `IPluginCatalog` | Read-only, immutable catalog snapshots and ID lookup |
| `PluginCatalog` | Host-owned validation, precedence, dependency, and lifecycle registry |
| `IPluginManager` | Installed search, compatibility check, repository search, and package operations |
| `IPluginRepository` | Future browse/search source independent of transport |
| `IPluginPackageService` | Future install/update/uninstall boundary |
| `IPluginSignatureVerifier` | Future package trust decision boundary |

## Manifest Example

```json
{
  "schemaVersion": 1,
  "id": "example.network-output",
  "name": "Example Network Output",
  "version": "1.2.0",
  "author": "Example Developer",
  "description": "Sends standardized output actions to a network endpoint.",
  "supportedApiVersion": {
    "minimum": "1.0",
    "maximumExclusive": "2.0"
  },
  "categories": ["output"],
  "dependencies": [],
  "entryAssembly": "Example.NetworkOutput.dll",
  "entryType": "Example.NetworkOutput.Plugin",
  "releaseNotes": "Initial SDK-compatible release."
}
```

The machine-readable contract is `docs/schema/hotas-plugin-manifest-v1.schema.json`.

## Version Rules

- Manifest schema and SDK API versions are separate.
- The host SDK API is currently `1.0`.
- API minimum is inclusive; API maximum is exclusive.
- Plugin/dependency versions use numeric major/minor/patch with optional semantic prerelease/build text.
- A future breaking public API increments the API major version.
- Additive members remain within the current major where binary/source compatibility allows.

## Dependencies

Dependencies use stable plugin IDs, inclusive minimum versions, optional exclusive maximum versions, and an optional flag. Missing optional dependencies do not disable a plugin. Missing required dependencies, incompatible versions, self-dependencies, duplicate declarations, and cycles do.

Internal registrations outrank bundled registrations, which outrank external manifests with the same ID. Lower-priority duplicates are disabled and remain visible in diagnostics.

## Output Contract

An output plugin:

1. publishes a `PluginManifest` containing the Output category;
2. initializes with `OutputPluginContext`;
3. starts independently;
4. consumes only actions addressed to its stable ID;
5. uses the shared scheduler for timed work;
6. resets all owned state and outputs;
7. reports immutable diagnostics;
8. stops and disposes without blocking other plugins.

Xbox and Keyboard use this exact contract. Their native implementation details remain private to `HOTASBridge.Output`.

## Isolation

The public Core API does not expose WPF view models, native device handles, concrete mapping state, profile stores, or output backends. Plugin exceptions are caught by the owning subsystem. Catalog validation happens before runtime registration; runtime failures update lifecycle to `Failed` and retain diagnostics.

External assembly loading, unload contexts, package extraction, signature trust stores, permissions, and sandboxing are not public Version 2 behavior. They require a later security review and API milestone.

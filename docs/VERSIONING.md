# Versioning

## Product Version

HOTASBridge follows Semantic Versioning for the application:

```text
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
```

- MAJOR changes when supported public contracts require incompatible migration.
- MINOR adds compatible product capability.
- PATCH fixes compatible defects.
- PRERELEASE identifies Development, Beta, or release-candidate artifacts.
- BUILD identifies source provenance, normally the Git commit.

The current development baseline is `0.26.0-dev`. The `0.x` line may evolve quickly, but profile/settings migrations remain mandatory.

## Central Build Properties

`Directory.Build.props` defines all assembly versions:

| Property | Default | Meaning |
| --- | --- | --- |
| `HotasBridgeVersionPrefix` | `0.26.0` | Product semantic version without suffix |
| `HotasBridgeReleaseChannel` | `Development` | Development, Beta, or Stable policy |
| `VersionSuffix` | `dev` for Development, `beta` for Beta | Prerelease identifier |
| `AssemblyVersion` | `0.26.0.0` | CLR assembly identity |
| `FileVersion` | `0.26.0.0` | Windows file version |
| `InformationalVersion` | SDK-generated | Product version plus source revision |

CI sets deterministic/continuous-integration metadata. The source revision is included in ProductVersion when available.

## Independent Version Domains

Application, data, and extension versions solve different compatibility problems and must not be synchronized artificially:

- Product version: executable release identity.
- Profile schema/profile revision: user configuration migration.
- Application settings schema: settings migration, currently v5.
- Workspace schema: dock layout compatibility.
- Recording schema: RuntimeSignal session compatibility.
- Plugin API version: host/plugin contract negotiation.
- Plugin manifest/package version: an individual plugin's release identity.

A product release may leave every data schema unchanged. A schema change requires migration and backup coverage but does not automatically require a product major version before `1.0`.

## Compatibility Rules

- Older supported profiles/settings are migrated forward automatically.
- Newer unknown schemas are never silently downgraded.
- Runtime state is never persisted as configuration.
- Stable feature keys are kebab-case identifiers and are not renamed casually.
- Public plugin API breaking changes require a negotiated API major version.
- Stable `1.x` patch releases do not intentionally break profile, settings, or plugin contracts.

## Version Update Checklist

1. Choose the next semantic version from the actual compatibility impact.
2. Update the central version prefix and release notes.
3. Confirm schema versions and migration notes independently.
4. Build with the intended release channel and suffix.
5. inspect ProductVersion/FileVersion on the generated executable and DLLs.
6. preserve Git commit provenance and deterministic build settings.
7. tag only the reviewed commit that produced the accepted artifact.

## Tag Convention

Stable and beta tags use `v` plus the complete public version, for example:

```text
v1.0.0
v1.1.0-beta.2
```

Development builds are not tagged routinely.

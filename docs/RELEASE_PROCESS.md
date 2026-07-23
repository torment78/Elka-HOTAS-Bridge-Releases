# Release Process

## Release Channels

HOTASBridge has three build channels:

| Channel | Purpose | Version example |
| --- | --- | --- |
| Development | Normal local and CI validation | `0.16.0-dev+<commit>` |
| Beta | External validation before stability | `0.19.0-beta.1+<commit>` |
| Stable | Supported public artifact | `1.0.0+<commit>` |

The default build is Development even when the MSBuild configuration is Release. A Release configuration therefore does not silently become a public Stable build.

## Build Commands

Development validation:

```powershell
dotnet build HOTASBridge.sln -c Debug
dotnet test HOTASBridge.sln -c Debug --no-build
dotnet build HOTASBridge.sln -c Release
```

Numbered beta candidate:

```powershell
dotnet build HOTASBridge.sln -c Release `
  -p:HotasBridgeVersionPrefix=0.19.0 `
  -p:HotasBridgeReleaseChannel=Beta `
  -p:VersionSuffix=beta.1
```

Stable candidate:

```powershell
dotnet build HOTASBridge.sln -c Release `
  -p:HotasBridgeVersionPrefix=1.0.0 `
  -p:HotasBridgeReleaseChannel=Stable `
  -p:VersionSuffix=
```

Only `Development`, `Beta`, and `Stable` are accepted channel values.

Unsigned installer candidate for local packaging checks:

```powershell
.\scripts\Build-Installer.ps1 -Configuration Release -Version 0.26.0
```

Signed promotion candidate:

```powershell
.\scripts\Build-Installer.ps1 -Configuration Release -Version 0.26.0 `
  -SigningCertificateThumbprint '<thumbprint>' -RequireSigning
.\scripts\Verify-ReleaseArtifacts.ps1 `
  -ManifestPath .\artifacts\installer\HOTASBridge-0.26.0-release.json `
  -RequireValidSignature
```

## Release Candidate Workflow

1. Select the release scope and freeze unrelated feature work.
2. Update `HotasBridgeVersionPrefix`, release notes, roadmap state, and migration documentation.
3. Review feature flags. Stable defaults must not expose unfinished Experimental, Hidden, or Debug-only capabilities.
4. Build and test Debug, Release Development, and the exact candidate command.
5. Validate existing profiles, application settings, workspace layouts, presets, and recordings against the candidate.
6. Execute hardware, driver, Safe Mode, emergency reset, suspend/resume, and clean shutdown procedures.
7. Run clean-machine installer, upgrade, repair, and uninstall tests.
8. Record performance baselines and compare them with the previous accepted build.
9. Review crash reports, known issues, third-party notices, and artifact contents.
10. Produce immutable artifacts, hashes, release notes, and a signed tag.

The automated portion is available through:

```powershell
.\scripts\Validate-Release.ps1
.\scripts\Validate-Release.ps1 -RequireReleaseReady
```

The installer build is a separate packaging gate because it requires Inno Setup and, for public promotion, the production signing certificate. `Validate-Release.ps1` verifies the source, scripts, release-artifact positive/negative cases, documentation, application output, tests, and coverage. `Build-Installer.ps1 -RequireSigning` fails closed, and `Invoke-DeploymentAcceptance.ps1` produces the retained-build/clean-machine evidence on a disposable Windows host.

The first command produces development evidence even when manual gates are pending. The promotion form fails while Project Health records blockers, pending required checks, incomplete documentation, or partial architecture acceptance.

## Architecture Review Gate

Every release validates `docs/architecture-reviews/architecture-review-schedule.json`. Version 1.0 cannot be marked release-ready until the `version-1-release` gate has an accepted record covering performance, maintainability, public APIs, plugin interfaces, runtime diagnostics, and backward compatibility, with linked ADRs. See [ARCHITECTURE_REVIEWS.md](ARCHITECTURE_REVIEWS.md).

## Mandatory Gates

A candidate cannot be promoted when any of the following is unresolved:

- solution build or automated test failure;
- profile/settings migration data loss;
- output that remains active after reset or shutdown;
- a crash in a supported startup or mapping path;
- an undocumented driver or runtime prerequisite;
- an enabled feature whose implementation is incomplete;
- performance regression outside the accepted release budget;
- missing release notes, migration notes, or hardware test evidence.

Warnings that do not block release must have an owner, impact statement, and target milestone.

## Feature Policy Review

- Stable features may be enabled by default in public builds.
- Beta features require explicit release approval before being enabled by default.
- Experimental features require the persisted developer opt-in and remain disabled by default.
- Debug-only features are also compile-time excluded from Release where applicable.
- Hidden features are omitted from normal settings and navigation.

Composition-level changes are applied only on startup. The UI records a restart requirement instead of rebuilding output/input services while they are active.

## Artifact Verification

Before publishing, record:

- ProductVersion, FileVersion, Git commit, build date, SDK, and target runtime;
- SHA-256 for every installer/archive;
- included output plugins and bundled driver/prerequisite versions;
- test totals and hardware matrix revision;
- upgrade source versions used in migration rehearsal.

The release is complete only after the published artifact is downloaded independently, its hash is verified, and that exact artifact passes the startup and mapping smoke tests.

## Patch Releases

Patch releases contain compatible bug fixes, diagnostics, documentation, and narrowly scoped hardware support. Profile or settings schema changes in a patch require a migration review and backup coverage. New experimental architecture belongs in a minor development line, not a stable patch.

## Rollback

Do not replace a published artifact in place. If a release is defective:

1. stop promotion and mark the release withdrawn;
2. publish the known impact and recovery steps;
3. preserve crash reports and the exact failing artifact;
4. release a higher patch version or restore the last accepted version;
5. retain profile/settings backups and never downgrade schemas destructively.

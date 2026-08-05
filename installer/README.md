# HOTASBridge Installer

`HOTASBridge.iss` is the source-controlled Inno Setup 6 package definition.

Build an explicitly unsigned development-channel installer and portable ZIP with:

```powershell
.\scripts\Build-DevArtifacts.ps1 -Version 0.29.0 -Iteration 1
```

Build a fail-closed signed release from a code-signing certificate already installed in the Windows certificate store:

```powershell
.\scripts\Build-Installer.ps1 -Configuration Release -Version 0.29.0 -ReleaseChannel Stable `
  -SigningCertificateThumbprint '<thumbprint>' `
  -SigningCertificateStoreLocation CurrentUser `
  -RequireSigning
```

The installer script publishes `win-x64`, verifies the application and bundled ViGEmBus payload, optionally signs first-party binaries, and writes versioned setup metadata. `Build-DevArtifacts.ps1` adds the framework-dependent ZIP, regenerates a two-artifact manifest/checksum set, and independently verifies it. Development artifacts are explicitly unsigned. Neither script accepts or logs a private-key password.

## Safety behavior

- The installer supports per-user and machine-wide modes through the Inno privilege selection dialog.
- Existing application files are upgraded in place under the stable AppId.
- Before an upgrade changes binaries, setup first tries to archive settings, profiles, workspaces, and previous application files under `%LOCALAPPDATA%\HOTASBridge\Backups\Deployment`.
- If installed files are locked, setup retries a user-data-only backup. Backup failure is logged but does not block an in-place repair or overwrite; Inno Setup still provides transaction rollback for application files.
- ViGEmBus is detected and reported, but never installed or removed by the installer. The explicit First Run Setup action owns driver installation.
- Uninstall preserves every user-data category unless the user selects individual categories for removal.
- Installation events are appended to `%LOCALAPPDATA%\HOTASBridge\Logs\installation.log`.

## Artifact verification

```powershell
.\scripts\Verify-ReleaseArtifacts.ps1 `
  -ManifestPath .\artifacts\installer\HOTASBridge-0.29.0-release.json `
  -RequireValidSignature
```

The verifier rejects tampering, unsafe or duplicate names, missing files, checksum-publication differences, signer changes, and unsigned release artifacts.

## Rollback

Inno Setup rolls back its active file transaction if setup fails. The pre-upgrade archive is an additional recovery artifact. With HOTASBridge closed, restore it using:

```powershell
& 'C:\Program Files\HOTASBridge\Deployment\Restore-DeploymentBackup.ps1' `
  -ArchivePath '<backup.zip>' `
  -InstallRoot 'C:\Program Files\HOTASBridge' `
  -RestoreApplicationFiles
```

Do not publish an installer until it is code-signed and the clean-machine procedures in `docs/INSTALLATION.md` pass.

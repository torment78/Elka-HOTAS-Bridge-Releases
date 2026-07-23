# HOTASBridge Installer

`HOTASBridge.iss` is the source-controlled Inno Setup 6 package definition.

Build an explicitly unsigned developer package with:

```powershell
.\scripts\Build-Installer.ps1 -Configuration Release -Version 0.26.0
```

Build a fail-closed signed release from a code-signing certificate already installed in the Windows certificate store:

```powershell
.\scripts\Build-Installer.ps1 -Configuration Release -Version 0.26.0 `
  -SigningCertificateThumbprint '<thumbprint>' `
  -SigningCertificateStoreLocation CurrentUser `
  -RequireSigning
```

The script publishes `win-x64`, verifies the application and bundled ViGEmBus payload, signs first-party binaries, lets Inno sign setup and its uninstaller, and writes the setup, release manifest, and SHA-256 sums to `artifacts\installer`. It never accepts or logs a private-key password.

## Safety behavior

- The installer supports per-user and machine-wide modes through the Inno privilege selection dialog.
- Existing application files are upgraded in place under the stable AppId.
- Before an upgrade changes binaries, settings, profiles, workspaces, and previous application files are archived under `%LOCALAPPDATA%\HOTASBridge\Backups\Deployment`.
- ViGEmBus is detected and reported, but never installed or removed by the installer. The explicit First Run Setup action owns driver installation.
- Uninstall preserves every user-data category unless the user selects individual categories for removal.
- Installation events are appended to `%LOCALAPPDATA%\HOTASBridge\Logs\installation.log`.

## Artifact verification

```powershell
.\scripts\Verify-ReleaseArtifacts.ps1 `
  -ManifestPath .\artifacts\installer\HOTASBridge-0.26.0-release.json `
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

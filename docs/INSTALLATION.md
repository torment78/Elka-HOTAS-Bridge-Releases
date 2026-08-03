# Elka HOTAS Bridge Installation

## Supported Environment

The current installer targets 64-bit Windows 10 and Windows 11. The framework-dependent package requires the .NET 10 Desktop Runtime. A separate Visual C++ runtime is not required by the current managed package.

ViGEmBus is optional for application startup. It is required only when Elka HOTAS Bridge must expose the generated state as a system-visible Xbox 360 controller.

## Installation Modes

The Inno Setup privilege dialog supports:

| Mode | Default location | Elevation |
| --- | --- | --- |
| Per user | User program-files location selected by Inno | Not required for application files |
| Machine wide | `C:\Program Files\HOTASBridge` | Windows administrator approval required |
| Portable | Reserved | Not available in 0.22 |

All modes use `%LOCALAPPDATA%\HOTASBridge` for user data in the current release. Profiles and UI workspaces are independent from application binaries.

## New Installation

1. Run the signed `HOTASBridge-<version>-Setup.exe` package.
2. Review Windows, .NET, Visual C++, ViGEmBus, and privilege status on the prerequisite page.
3. Choose per-user or machine-wide installation.
4. Complete installation and launch Elka HOTAS Bridge.
5. Follow First Run Setup, or choose **Skip setup** and reopen it later from **About**.

The First Run Setup sequence is:

1. Welcome and prerequisites.
2. Detect connected devices.
3. Verify the virtual Xbox driver.
4. Select input devices.
5. Create or choose a profile.
6. Test meaningful device input.
7. Create an optional starter Xbox mapping.
8. Verify Xbox output with an explicit A-button test.
9. Save and finish.

The input test reads `RuntimeSignal` publications from the selected devices. It does not poll hardware from WPF. The Xbox test goes through `IOutputManager`, releases the test button, resets output state, and disconnects.

## Virtual Xbox Driver

The application and installer detect ViGEmBus through one shared driver-status service. If the driver is missing:

- HOTASBridge still starts;
- device inspection, profiles, mapping editing, keyboard output, and diagnostics remain available;
- Xbox output reports `Driver Missing`;
- the First Run Setup page can launch the bundled ViGEmBus 1.22.0 package.

Driver installation starts only after **Install bundled driver...** is pressed. Windows displays its normal administrator prompt. Neither the application installer nor the uninstaller silently installs or removes a kernel driver.

## Existing Installations

The stable Inno AppId detects an existing HOTASBridge installation. Running a newer package upgrades the application in place. Running the same package supports repair through Inno's normal maintenance behavior.

Before an upgrade changes binaries, setup creates a deployment backup containing:

- profiles;
- application settings;
- workspace layouts;
- previous application files;
- source version and timestamp manifest.

Backups are written under `%LOCALAPPDATA%\HOTASBridge\Backups\Deployment`.

If previous application files cannot be copied, setup retries with profiles, settings, and workspace data only. If that retry also fails, the failure is logged and setup continues with Inno Setup's transaction rollback protection. A failed optional backup therefore does not prevent repairing or overwriting an existing installation. HOTASBridge should be closed when prompted so setup can replace active binaries; uninstalling the previous version is not required.

An existing installation with settings, profiles, or workspace data does not open First Run Setup automatically. The wizard remains available from **About**.

## Uninstall

Application files are removed by Inno Setup. User data is preserved by default. The uninstaller asks separately whether to remove:

- profiles;
- settings and workspace layouts;
- logs and diagnostics;
- backups.

Unselected data remains untouched. The ViGEmBus driver is not removed because it may be shared by other applications.

## Rollback

Inno rolls back its active file transaction when setup fails. A pre-upgrade archive provides an additional manual recovery path. Close HOTASBridge before restoring:

```powershell
& 'C:\Program Files\HOTASBridge\Deployment\Restore-DeploymentBackup.ps1' `
  -ArchivePath '<backup.zip>' `
  -InstallRoot 'C:\Program Files\HOTASBridge' `
  -RestoreApplicationFiles
```

Use `-WhatIf` first when reviewing a restore destination. The script validates archive paths and the versioned manifest before copying files.

## Logs

Application installation events are appended to:

```text
%LOCALAPPDATA%\HOTASBridge\Logs\installation.log
```

For a full Inno transcript, launch setup with `/LOG="<path>"`.

## Current Manual Gates

Before public distribution, validate the installer on clean Windows 10 and Windows 11 virtual machines for:

- per-user install;
- machine-wide install;
- missing .NET handling;
- ViGEmBus present and absent;
- repair and upgrade;
- automatic pre-upgrade backup;
- rollback;
- category-specific uninstall preservation;
- code signature and checksum.

Release engineering provides an automated acceptance runner, but it is deliberately blocked on normal workstations:

```powershell
.\scripts\Invoke-DeploymentAcceptance.ps1 `
  -CandidateInstallerPath '<candidate-setup.exe>' `
  -CandidateManifestPath '<candidate-release.json>' `
  -RetainedInstallerPath '<retained-setup.exe>' `
  -IncludeUninstallChoiceMatrix `
  -DisposableMachineConfirmed
```

Run it only on a disposable clean Windows image. It requires signed packages and emits `deployment-acceptance.json` plus an Inno log for every operation.

![Elka HOTAS Bridge](assets/elka-hotas-bridge-wordmark.png)

# Elka HOTAS Bridge

Elka HOTAS Bridge is a Windows desktop application for translating HOTAS, joystick,
throttle, pedal, button-box, keyboard, and mouse inputs into virtual Xbox 360,
keyboard, and mouse outputs.

This public repository contains documentation and downloadable release packages.
It intentionally does not contain the Elka HOTAS Bridge source code.

## Download

Open the repository's **Releases** section and select the latest test release:

- `HOTASBridge-0.26.0-Setup.exe` - recommended installer.
- `HOTASBridge-0.26.0-win-x64.zip` - framework-dependent portable package.

This is a `0.26.0` test release for Windows 10/11 x64. It requires the
.NET 10 Desktop Runtime.

The current signed test-release files retain their established `HOTASBridge` filenames. The visible product name is now **Elka HOTAS Bridge**; future signed builds will carry the new in-app name and dark application icon while retaining compatible internal paths.

## Package Trust

The installer and all first-party Elka HOTAS Bridge executable files are
Authenticode-signed by Mikael Frisberg and timestamped by SSL.com.

The ZIP format does not support Windows Authenticode. The ZIP contains the same
signed application binaries and is covered by the published SHA-256 checksum.
Use `HOTASBridge-0.26.0-SHA256SUMS.txt` to verify both downloads.

The bundled ViGEmBus installer retains its original valid signature from
Nefarius Software Solutions. Elka HOTAS Bridge detects the virtual Xbox driver and
never installs or removes it silently.

## Start Here

- [User Guide](docs/USER_GUIDE.md)
- [Branding and compatibility names](docs/BRANDING.md)
- [Installation](docs/INSTALLATION.md)
- [Troubleshooting](docs/TROUBLESHOOTING.md)
- [Profiles](docs/PROFILE_SYSTEM.md)
- [Mapping Engine](docs/MAPPING_ENGINE.md)
- [Keyboard Output](docs/KEYBOARD_OUTPUT.md)
- [Output System](docs/OUTPUT_SYSTEM.md)
- [Macro Editor](docs/MACRO_EDITOR.md)
- [Device Inspector](docs/DEVICE_INSPECTOR.md)
- [Navigation](docs/NAVIGATION.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Release Notes](docs/RELEASE_NOTES.md)

## Important

Elka HOTAS Bridge is still a test release. Back up important profiles before
upgrading and review the release notes and known limitations before use.

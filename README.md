![Elka HOTAS Bridge](assets/elka-hotas-bridge-wordmark.png)

# Elka HOTAS Bridge

Elka HOTAS Bridge is a Windows desktop application for translating HOTAS, joystick,
throttle, pedal, button-box, keyboard, and mouse inputs into virtual Xbox 360,
keyboard, and mouse outputs.

This public repository contains documentation and downloadable release packages.
It intentionally does not contain the Elka HOTAS Bridge source code.

## Download

Open the repository's **Releases** section and select **Elka HOTAS Bridge
0.29.0 Dev Release 2**:

- `HOTASBridge-0.29.0-dev.2-Setup.exe` - unsigned development installer.
- `HOTASBridge-0.29.0-dev.2-win-x64.zip` - unsigned framework-dependent
  portable package.

This is a `0.29.0-dev.2` development release for Windows 10/11 x64. It requires
the .NET 10 Desktop Runtime and is published as a normal, non-latest GitHub
release so testers can find it without replacing the signed stable download.

Release builds focus on the stable Easy Mode workflow. Advanced Mode is under
construction and remains available only in developer Debug builds. Compatible
internal executable and download names remain `HOTASBridge`.

## Package Trust

Dev 2 is deliberately unsigned. Windows may display an **Unknown Publisher**
warning. Use the SHA-256 checksum file attached to the release to verify the
installer and ZIP before running them.

The signed `0.28.0` test release remains available separately for users who do
not need the Dev 2 features.

The bundled ViGEmBus installer retains its original valid signature from
Nefarius Software Solutions. Elka HOTAS Bridge detects the virtual Xbox driver and
never installs or removes it silently.

Dev 2 also contains the optional HIDMaestro Xbox-family backend. It is disabled
by default. Its driver is installed or repaired only after explicit confirmation
from Output Monitor and a Windows elevation prompt.

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

Elka HOTAS Bridge Dev 2 is a development build. Back up important profiles
before upgrading and review the release notes and known limitations before use.

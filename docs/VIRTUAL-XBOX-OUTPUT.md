# Virtual Xbox Output

HOTASBridge isolates virtual-controller output behind `IVirtualGamepadOutput`.

The selected first backend is ViGEm's Xbox 360 output path because it exposes a standard XInput controller that most Windows games understand. ViGEmBus itself is retired/end-of-life, but it remains the practical free redistributable option for an Xbox 360 virtual controller in this milestone. Newer Nefarius virtual gamepad offerings are commercial.

Current implementation preserves `VirtualXboxOutputService` behind `XboxOutputPlugin` and the common Output Manager.

- client library: `Nefarius.ViGEm.Client` version `1.21.256`
- driver package: official ViGEmBus `1.22.0`

Current implementation:

- checks for a ViGEmBus-compatible service/installation
- creates one ViGEm Xbox 360 controller when mapping starts and the driver is installed
- submits sticks, triggers, D-pad, face buttons, bumpers, stick clicks, Start, Back, and Guide state
- neutralizes and disconnects the virtual controller when mapping stops
- falls back to an internal Xbox state monitor when the driver is missing or fails
- bundles the official ViGEmBus 1.22.0 setup executable with the app output
- offers driver verification and an explicitly confirmed elevated installer from the First Run Wizard
- keeps driver-specific calls isolated in `HOTASBridge.Output`
- exposes Xbox state/health/rate/errors plus cumulative connect, submit, and cleanup failure counters through manager diagnostics and Output Monitor

## Driver packaging

HOTASBridge includes the official ViGEmBus 1.22.0 setup executable as app content:

- source file: `third_party\ViGEmBus\ViGEmBus_1.22.0_x64_x86_arm64.exe`
- build output: `Drivers\ViGEmBus\ViGEmBus_1.22.0_x64_x86_arm64.exe`

The shared driver service checks ViGEmBus registry/service evidence and default driver paths without blocking startup. A new installation can open the First Run Wizard after the shell is ready. Choosing **Install Driver** starts the bundled installer with Windows elevation/UAC; existing installations skip the wizard and can reopen it from **About**.

The installer is not hidden or silently forced because ViGEmBus is a kernel driver. Users still get the normal Windows administrator approval flow.

## Offline License Notices

About is available in both Easy and Advanced modes. **View bundled ViGEm license notices** opens an internal read-only window; it never launches a browser.

- ViGEmBus 1.22.0: BSD 3-Clause license, bundled from `third_party\ViGEmBus\LICENSE.txt`.
- Nefarius.ViGEm.Client 1.21.256: MIT license, bundled from `third_party\ViGEm.Client\LICENSE.txt`.

Build output copies both notices to `ThirdPartyLicenses`. The viewer reports a missing file explicitly instead of downloading one.

## Xbox controller target

Xbox 360/XInput is the best first target for game compatibility. Xbox One and Xbox Series controllers still use the same practical XInput shape for most games, so they do not give HOTASBridge more ordinary game-facing buttons. Extra HOTAS controls should be handled with profiles, shift layers, toggles, long press/double press behavior, or mapped to keyboard/mouse outputs in a later backend.

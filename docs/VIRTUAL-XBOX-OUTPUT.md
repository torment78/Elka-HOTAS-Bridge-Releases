# Virtual Xbox Output

HOTASBridge isolates virtual-controller output behind `IVirtualGamepadOutput`.

The stable default backend remains ViGEm's Xbox 360 output path because it exposes a standard XInput controller that most Windows games understand. ViGEmBus itself is retired/end-of-life, but existing profiles and installations remain supported.

An optional Beta backend uses HIDMaestro 1.4.3 and a curated set of its built-in Xbox profiles. It can present Xbox 360 wired/wireless-adapter, Xbox One, One S, Series X|S, Elite, or Adaptive identities through a user-mode UMDF2 virtual HID stack. HIDMaestro is disabled by default and is loaded only when the **Xbox Family Output** feature flag is enabled.

Both backends use `XboxOutputPlugin`, `XboxState`, and the common Output Manager. Existing mappings continue to store `xbox360` as their logical Xbox target. When a profile selects Xbox-family output, Output Manager redirects that Xbox action batch to `xboxone` before plugin processing; profile files and mappings do not need migration. A profile can enable only one Xbox backend at a time.

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

## HIDMaestro Xbox-family backend

1. In **Settings > Features**, enable **Xbox Family Output** and restart the application.
2. Open **Output Monitor** and choose **Install / Repair Xbox Driver...**.
3. Review the warning and approve Windows elevation. HIDMaestro installs a locally trusted certificate and its user-mode virtual HID driver from payloads embedded in `HIDMaestro.Core.dll`.
4. While mapping is stopped, choose the required **Controller identity**. The selection is stored in the active profile's existing output settings dictionary and requires no schema migration.
5. In **Active outputs**, enable **Xbox Family Output (Experimental)**. This disables and grays out Xbox 360 output for that profile without deleting or rewriting mappings. Uncheck the active backend before switching back.
6. Start mapping. The selected virtual Xbox-family controller exists only while the output session is running.

The identity dropdown changes the HIDMaestro profile used to create the next controller; it does not merely rename the monitor card. Identity changes are rejected while mapping is active. The install action is explicit and idempotent. Normal startup, feature enablement, profile loading, and Start Mapping never approve driver installation on the user's behalf. If the driver is missing, the plugin reports `DriverMissing` and the application continues running keyboard, mouse, and other enabled outputs.

The SDK is vendored at `third_party\HIDMaestro\HIDMaestro.Core.dll`. The official 1.4.3 release archive SHA-256 is `35106BA46CFA90E9EBBEF365D69EBC361B4AAE46420FAA78233F48802DF9ADBA`; the extracted vendored DLL SHA-256 is `C54F60387CFFF26BE9FA3A7842C45D17122DD8DCA73ADF8BAFA1AEB9A60C2EBD`.

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
- HIDMaestro 1.4.3: MIT license and bundled dependency notices from `third_party\HIDMaestro`.

Build output copies all notices to `ThirdPartyLicenses`. The viewer reports a missing file explicitly instead of downloading one.

## Xbox controller target

Xbox 360/ViGEm remains the compatibility-first default. Xbox-family/HIDMaestro output is available for games or diagnostics that require a newer controller identity, but it does not add ordinary XInput buttons to the HOTAS mapping model. Extra HOTAS controls remain available through profiles, macros, shift layers, keyboard, and mouse outputs.

# Device Input

The first implementation includes a `WindowsRawInputDeviceProvider` and `SimulatedInputService`.

Raw Input is used for discovery because it can identify HID joystick/gamepad-class devices without depending on the legacy Game Controllers panel. Each detected HID device stores:

- device path
- vendor ID
- product ID
- usage page
- usage ID
- physical or virtual classification
- stable identifier

Simulation devices expose high button counts, axes, hats, encoders, and switches so UI and mapping work can continue without hardware.

## Next physical-input milestone

The next layer should add HID report parsing using preparsed data from Windows HID APIs or a maintained wrapper. That layer should produce `InputEvent` instances with the same shape already used by simulation, so the mapping engine remains source-agnostic.

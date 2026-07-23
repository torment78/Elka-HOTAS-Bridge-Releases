# Troubleshooting

## Git application error during Debug startup

Current Debug builds do not launch `git.exe`. The Developer Dashboard reads branch and commit metadata directly from `.git` repository files and displays `unknown` if metadata is unavailable. Git installation health therefore cannot block or interrupt HOTASBridge startup.

## No devices appear

Use the manual Refresh button. Simulation devices should always appear. If physical devices are absent, confirm Windows can see the HID device and that the device usage is joystick or gamepad class.

## Duplicate input warning

The app may warn when a physical device and a virtual copy are both selected. This is common with vJoy, SimAppPro, TARGET, and other preprocessing chains. Keep only one path enabled unless you intentionally need both.

## Virtual Xbox controller is not visible in games

HOTASBridge creates the virtual Xbox 360 controller only while mapping is running and only when the ViGEmBus driver is installed. Open **About > Open Setup Wizard**, verify the driver step, and choose **Install Driver** if needed. Installation is never silent: approve Windows elevation, complete the visible ViGEmBus installer, return to the wizard, and verify again before starting mapping.

## Logs and profiles

Logs: `%LOCALAPPDATA%\HOTASBridge\Logs`

Profiles: `%LOCALAPPDATA%\HOTASBridge\Profiles`

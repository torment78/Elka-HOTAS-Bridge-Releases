# Elka HOTAS Bridge User Guide

## What Elka HOTAS Bridge Does

Elka HOTAS Bridge combines controls from one or more Windows HOTAS-class devices and routes configured mappings to a virtual Xbox 360 controller or Windows keyboard output.

## First Start

1. Start Elka HOTAS Bridge normally. A new installation opens the skippable **First Run Wizard** after the main window is ready.
2. Follow the wizard to detect and select devices, choose a profile, test input, create an optional starter mapping, and verify Xbox output.
3. If ViGEmBus is missing and Xbox output is required, choose **Install Driver** in the wizard. Elka HOTAS Bridge never installs the kernel driver silently; Windows displays the normal UAC and installer UI.
4. Existing installations skip the wizard automatically. Reopen it later from **About**.
5. Use `HOTASBridge.App.exe --safe-mode` only when recovering from an output or profile problem; Safe Mode suppresses automatic setup and all outputs.

Safe Mode loads profiles and diagnostics while disabling output plugins and automatic mapping.

## Devices

The Devices page separates physical, virtual, connected, missing, and unsupported devices. A device must be selected for the active profile before its controls appear in Mapping Editor.

Device Inspector provides one tab per selected device. It reads the Runtime Signal Cache, not the hardware API directly. Use it to verify raw and normalized values, detect control names, freeze the display, review events, and export diagnostics. Hats use a live 3x3 direction grid, encoders show CW/CCW pulse counts and last direction, and switches show a normalized position track with current/previous values.

Simulation devices are optional development and demonstration sources. They are not required for physical HID discovery.

## Profiles

A profile stores selected device identities, mappings, transform chains, and output configuration. Runtime button, timer, PWM, and output states are never saved.

Available workflows include:

- New profile or template
- Open and Save
- Save As
- Import with compatibility preview and Export
- Search and filter local profiles
- Export device-neutral templates or selected content
- Compare profiles, merge selected changes, and export reports
- Duplicate and Rename
- Configurable Auto Save

Profile Health reports missing devices, duplicate mappings, conflicting outputs, migration state, and validation warnings. Warnings do not silently modify the profile.

Import always shows a summary and compatibility report before writing. Replacing the active profile creates a backup. Profile comparison merges right-side changes into the active left-side profile only when selected; use Save afterward to persist the merge.

## Creating Mappings

1. Open **Mapping Editor**.
2. Select a device and choose a live input control, or use Learn Input.
3. Select Xbox or Keyboard output.
4. Choose the target control or capture a keyboard shortcut.
5. Configure transforms and mapping behavior.
6. Create the mapping, then save the profile.

Mapping Explorer supports search, grouping, sorting, multi-selection, duplicate, enable, disable, delete, and export. Double-clicking a row opens it in Mapping Editor.

Node Editor is an optional Beta visual view of the same mappings. Select a mapping, then add compatible transforms, apply a template, edit settings, reorder or duplicate nodes, and inspect live values and timing. Changes appear in Mapping Editor and Transform Editor immediately because all three edit the same profile mapping. Ctrl+mouse wheel zooms, middle-drag pans, and dragging a node header changes only the current visual layout.

## Creating Macros

1. Open **Macro Editor** and select **Add macro**. New macros start disabled.
2. In Easy mode, name the macro, select the trigger field, then press a HOTAS button, key, mouse button, or chord such as Ctrl+A or Ctrl+click. Press Escape to cancel learning.
3. Choose **Timeline** for the same action list every time, **Sequence** to cycle forward through up to five steps, or **PingPong** to cycle forward and then backward.
4. Add quick key presses, toggles, mouse clicks, or delays; alternatively select **Start** under Record and use F12 when capture is complete. The capture list updates live with actions and delays. With mapping running, mapped HOTAS button outputs are recorded too; continuous axes are excluded.
5. Select an action to edit its output type, control, timing, X/Y movement, or wheel value. Use Up and Down to reorder it.
6. Resolve the validation summary, enable the macro, and select **Save Profile**.
7. Use **Import** or **Export** to move one `.hotasmacro` package between profiles.

Select **Advanced** for conditions, runtime variables, threshold/zone/timer triggers, mapping/profile commands, priority, notes, and iteration safeguards. Easy and Advanced edit the same macro; changing modes does not duplicate it.

Macro outputs execute only while mapping is running. **Macro Debugger** displays execution after mapping starts.

## Transforms And Curves

Axis Curves displays raw input, processed output, live position, deadzones, saturation, inversion, and the selected response curve. Transform Editor manages ordered transform chains and reusable presets.

To smooth a noisy axis, open **Transform Editor**, select the mapping, and select or add **Filter** in its transform chain. Choose Moving Average, Exponential Smoothing, or Median Filter, adjust the displayed alpha or window control, then choose **Apply live**. The change rebuilds only that mapping; save the profile when the result is satisfactory.

To create an Analog PWM keyboard mapping:

1. In **Mapping Editor**, select an axis input and Keyboard output.
2. Capture the key for positive movement, then enable **Analog PWM**.
3. Start with Balanced, Responsive, or Smooth and adjust the timing, thresholds, response, and smoothing when needed.
4. For a centered axis, enable two-direction mode and capture a different negative-direction key.
5. Create or update the mapping, then save the profile.

The editor blocks invalid timing and identical bipolar keys. Each mapping owns its settings; changing one PWM mapping does not affect another.

## Starting Output

Select **Start Mapping** to begin input processing and output. The virtual Xbox 360 controller exists only while the Xbox output plugin is running. Stopping mapping disconnects the virtual controller and neutralizes output.

Output Monitor displays plugin health, output rate, queue depth, errors, Xbox state, held keyboard keys, PWM jobs, and reset controls. The Xbox card also shows cumulative backend connection, submission, and cleanup failures plus the last failed operation for the current application session.

Use **Emergency Reset** whenever output appears stuck. It releases keyboard keys, resets Xbox controls, cancels timers, stops PWM, and clears runtime output state.

## Diagnostics

- **Dashboard** summarizes the active profile, runtime state, recent input, and generated Xbox state.
- **Signal Flow Inspector** follows one control through processing stages.
- **Diagnostics** shows runtime messages and warnings.
- **Project Health** shows development and release-readiness evidence.
- Debug builds also include Developer Dashboard, Performance Profiler, and Test Runner.

## AI Explain

Use `AI Explain` on Device Inspector, Mapping Editor, Signal Flow Inspector, Profile Health, or an Output Monitor plugin to summarize the selected object from current local evidence. The dialog shows runtime state, active transforms, diagnostics, and evidence-based suggestions.

The current advisor is offline and read-only. It cannot save a profile, change a mapping, reset output, or install a driver. Review suggestions yourself; every proposed change requires confirmation outside the explanation. `Copy Explanation` places a timestamped text report on the Windows clipboard for support or bug reports.

## Activate A Profile For A Game

1. Open **Profiles** and select the profile to associate.
2. Under **Target application**, choose **Browse EXE**, or choose **Refresh processes**, select a running application, and click **Use selected process**.
3. Save the profile.
4. The next newly detected matching application activates that profile automatically.

Use **Clear** to disable the association. An exact executable path is preferred; a simple executable name also works. When two profiles match with equal strength, HOTASBridge reports the conflict and leaves the current profile active. Automatic activation is disabled in Safe Mode and never starts mapping or launches the application.

The process list is local-only. HOTASBridge does not inspect command lines or send process information anywhere.

## Settings And Workspaces

Settings contain General, Devices, Profiles, Output, Diagnostics, Developer, and Advanced categories. Diagnostics includes log retention from 1 to 365 days, with a 14-day default. Feature-policy changes that affect service composition require restart.

Workspace layouts control dock placement independently from profiles. Built-in layouts are Flight Setup, Mapping Mode, Diagnostics, and Development. Custom layouts can be saved and restored without changing hardware behavior.

## Data Locations

HOTASBridge stores profiles, settings, workspace layouts, logs, backups, diagnostics, recordings, crash reports, and temporary files in separate folders below the application data root. The normal root is under the current user's local application data directory.

## Recovery

After an interrupted session, HOTASBridge can restore the previous profile, selected devices, and workspace. Output state is never restored.

For startup failures:

1. Start with `--safe-mode`.
2. Review Dashboard, Output Monitor, Project Health, and Diagnostics.
3. Use Emergency Reset.
4. Validate the profile and driver status.
5. Export diagnostics before changing configuration.

See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for device, driver, mapping, and profile-specific procedures.

## Current Release Limits

Version `0.26.0-dev` is a development milestone, not the supported 1.0 release. Certificate-backed signing has been completed; formal physical-hardware sign-off, clean-machine installer validation, retained-build migration rehearsal, long-duration soak evidence, and the final Version 1.0 architecture review remain open in [PROJECT_HEALTH.md](PROJECT_HEALTH.md).

## Easy Setup Workflow

1. The application starts in locked Easy Mode. To work on deeper diagnostics or editors, open Settings > General and select **Unlock Advanced Mode**. Use **Lock Advanced Mode** there to return immediately to the focused setup navigation.
2. Add one or more devices under Input Devices. Input testing works before Start Mapping.
3. Use Test Inputs to verify axes, buttons, hats/D-pads, encoders, switches, and unknown controls per device tab.
4. In Mappings select an input, choose Xbox, Keyboard, or Mouse, then click the visual target or its list fallback.
5. Easy Mode can apply common presets; each preset creates visible normal mappings.
6. Use Outputs to inspect only the output types enabled by the active profile.
7. Start Mapping to connect generated outputs. Emergency Release neutralizes Xbox and releases keyboard/mouse state.

Mouse and keyboard injection uses Windows SendInput. Elevated or protected applications may reject injected input; HOTASBridge does not bypass those restrictions. See [HAT-COMPATIBILITY.md](HAT-COMPATIBILITY.md) for hardware validation status.

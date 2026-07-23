# Easy Input And Output Workflow Completion Report

## Scope

This milestone adds Easy Mode, provider-aware hat normalization, mouse output, visual keyboard and mouse mapping, output-monitor filtering, and guided mapping presets to the existing HOTASBridge runtime. It extends the current profile, mapping, scheduler, output-plugin, diagnostics, and WPF systems rather than introducing parallel implementations.

Existing HOTAS-to-Xbox behavior, keyboard output, profile persistence, runtime signals, advanced navigation, output scheduling, emergency reset, and feature flags remain in place.

## Initial Assessment

| Requirement area | Initial state | Milestone result |
| --- | --- | --- |
| Physical input while mapping is stopped | Partially implemented | Physical providers remain active for testing and editing; Start Mapping controls output/runtime activation. |
| Easy and Advanced presentation | Missing | Added one application setting and a presentation policy over the shared runtime and profile model. |
| Guided setup | Partially implemented | Existing first-run wizard now includes output selection and output-aware starter mapping/testing. |
| Multi-device selection and diagnostics | Implemented | Preserved and enriched with provider correlation, friendly capability counts, and hat diagnostics. |
| Hat processing | Provider-specific and partially normalized | Added immutable normalized hat state, provider descriptors, stable publication, and mapping adaptation. |
| PlayStation D-pad support | No dedicated validation | Uses the normalized hat path and has simulated compatibility coverage. |
| Mouse output | Missing | Added a tracked scheduler-backed output plugin using Windows SendInput. |
| Visual keyboard and mouse selection | Missing | Added data-driven interactive visualizers with accessible list fallbacks. |
| Output monitor | Xbox and keyboard focused | Filters profile-enabled panels and supports Xbox, keyboard, and mouse state. |
| Profile compatibility | Schema v6, settings v3 | Migrates profiles to v7 and application settings to v5. |
| Major future platform work | Already present behind feature flags | Left unchanged; this milestone remains focused on practical mapping workflows. |

## Hat Investigation And Normalization

The code-path investigation found four interacting causes for unreliable or confusing hat behavior:

1. Providers can expose incompatible encodings: DirectInput hundredths of degrees, zero-based HID indexes, one-based HID indexes, and individual buttons.
2. Centered/null values depend on provider metadata and logical ranges; a global numeric interpretation cannot distinguish all valid cases.
3. Provider-less legacy events previously depended on inferred values, while live providers did not carry enough explicit decoding metadata through the runtime.
4. Repeated identical direction reports and likely duplicate API representations could create unnecessary transitions or ambiguous device choices.

The new `HatDescriptor`, immutable `HatState`, and `HatNormalizer` preserve the raw provider value while publishing a shared direction. Live Windows HID and simulation providers declare their encoding. Legacy direction adaptation is isolated explicitly for old API callers instead of being applied globally.

Supported normalized inputs are:

- DirectInput-style `-1` or `0xFFFFFFFF` center and `0` through `31500` directions;
- HID zero-based `0-7` ranges with provider-declared null behavior;
- HID one-based `1-8` ranges with zero centered;
- four-way and eight-way direction counts;
- individual direction buttons;
- a separate center-button state for five-way or nine-way controls;
- simulated hats and PlayStation-style D-pad/POV input.

Unknown or unusual vendor values retain their raw diagnostics and are marked invalid/centered instead of being assigned a guessed direction. A provider adapter can add an explicit encoding when that hardware is validated.

Hat mappings now support independent directions, a separate center press, dedicated diagonals, combined cardinal decomposition, and ignored diagonals. Xbox D-pad mappings default to combined cardinal behavior so diagonals activate the correct two outputs.

## Provider Correlation

`CompositeInputProvider` now correlates strong same-path representations, selects a live/control-enumerating provider by precedence, records the provider set and warning on the device model, logs the correlation once, and exposes a telemetry count. An Advanced setting allows likely duplicates to remain selectable with distinct stable IDs.

Processed virtual devices such as vJoy remain independent when their device path is distinct. Correlation deliberately does not merge weak name-only matches.

## Easy Mode And Setup

Easy Mode exposes Home, Input Devices, Test Inputs, Mappings, Outputs, Profiles, and Settings. Advanced Mode restores the complete navigation. Switching modes changes presentation only and never rewrites a profile or runtime mapping.

Easy Mode provides:

- plain-language home status and actions;
- an advanced-content notice when a profile contains custom transforms, conditions, macros, or non-default layers;
- physical input testing before output mapping is started;
- output selection in the first-run workflow;
- backward/forward non-destructive setup steps;
- ordinary mapping presets for hats, axes, buttons, Xbox, keyboard, mouse, and PlayStation-style D-pads;
- relevant processing controls while the full transform editor remains available in Advanced Mode.

The presets create normal `InputMapping` records and use the existing mapping engine. There is no Easy Mode runtime or profile fork.

## Mouse Output

The new Mouse Output plugin participates in the existing output-manager lifecycle and supports:

- relative horizontal and vertical movement;
- proportional axis-to-pointer velocity;
- left, right, middle, X1, and X2 buttons;
- vertical and horizontal wheel output;
- held hat/D-pad movement;
- independent horizontal and vertical speed;
- deadzone, maximum speed, optional acceleration, acceleration curve, initial delay, update interval, and time to maximum speed;
- diagonal normalization, inversion, and slow/fast modifiers;
- tracked diagnostics for active directions, velocity, movement mappings, buttons, wheel outputs, relative movement, and output rate.

One shared output-scheduler job drives all pointer movement; no mapping owns a thread or permanent loop. Centering a hat removes its contribution immediately. Reset, mapping stop, profile switch, shutdown, and Emergency Release cancel movement and wheel schedules, clear state, and release held buttons.

Absolute positioning remains reserved for a future requirement. HOTASBridge does not attempt to bypass elevation boundaries, protected applications, or anti-cheat restrictions.

## Visual Outputs

The Mapping Editor now switches among Xbox Controller, Keyboard, and Mouse output modes without clearing the selected input.

The data-driven keyboard supports US ANSI and Nordic ISO definitions plus a Follow Windows preference. Mappings retain virtual-key, scan-code, and modifier identity rather than relying on the displayed character. The visual keyboard displays unassigned, selected, assigned, conflicting, generated, and modifier states, and assigned-key tooltips identify mappings. A searchable/list selection remains available for keyboard accessibility.

The mouse visualizer supports pointer directions, five buttons, and four wheel directions. The Output Monitor displays only output types enabled or used by the active profile, and its Xbox, keyboard, and mouse panels are independently collapsible.

## Persistence And Compatibility

Application version is `0.26.0-dev`.

Profile schema v7 adds mouse output configuration, normalized hat direction, center-button mapping, and diagonal behavior. Migration preserves existing Xbox and keyboard mappings and infers cardinal direction metadata for older Xbox D-pad mappings. Existing profile backup-before-migration behavior is retained.

Application settings schema v5 adds interface mode, visual keyboard layout, and the duplicate-provider override. Existing settings migrate to Advanced Mode so upgrades retain the complete interface; new installations default to Easy Mode and Follow Windows keyboard layout.

Plugin API and Script API remain at 1.0. Existing profile, plugin, macro, node, and scripting configuration is not removed or simplified.

## Files Added

Core/runtime additions:

- `EasyMappingPresets.cs`
- `HatModels.cs`
- `KeyboardLayouts.cs`
- `PresentationPolicies.cs`
- `HatMappingSignalAdapter.cs`
- `MouseInputInjector.cs`
- `MouseOutputPlugin.cs`

WPF additions:

- `KeyboardVisualizer.cs`
- `MouseVisualizer.cs`

Test additions:

- `EasyMappingPresetTests.cs`
- `HatMappingTests.cs`
- `HatNormalizationTests.cs`
- `MouseMappingTests.cs`
- `PresentationPolicyTests.cs`
- `ProfileMilestoneMigrationTests.cs`
- `ApplicationSettingsMilestoneTests.cs`
- `EasyOutputWorkflowIntegrationTests.cs`
- `MouseOutputIntegrationTests.cs`

Documentation and governance additions:

- `HAT-COMPATIBILITY.md`
- `hotas-profile-v7.schema.json`
- ADR 0003, normalized hats and output plugins
- July 2026 Easy Input/Output architecture review
- this completion report

## Files Modified

Runtime and persistence changes are concentrated in the existing input models/providers, runtime signal engine, mapping engine, profile/configuration/migration models, profile comparison, output diagnostics, feature flags, and JSON settings store.

WPF changes are concentrated in application composition/resources, Main Window mapping and settings surfaces, Device Inspector view models, First Run Wizard, Output Monitor, deployment/startup view models, and output editor mode selection.

Architecture, input, device, profile, output, keyboard, UI, user-guide, migration, release, versioning, feature-matrix, project-health, changelog, ADR, and architecture-review documents were updated with the implemented behavior and compatibility boundaries.

## Automated Validation

Measured on 2026-07-18 from the completed working tree:

| Check | Result |
| --- | --- |
| Debug build | Passed, 0 warnings, 0 errors |
| Release build | Passed, 0 warnings, 0 errors |
| Core tests | 161 passed |
| Integration tests | 73 passed |
| Architecture-validator tests | 9 passed |
| Scripting tests | 8 passed |
| Total automated tests | 251 passed, 0 failed, 0 skipped |
| Net new tests | 43 |
| Merged runtime line coverage | 56.60 percent, 13,051 of 23,059 lines |
| Architecture validator | 0 errors, 2 tracked warnings, 9 tracked suggestions |
| Architecture-review schedule | Passed, 2 satisfied and 4 planned; schema reviewed through v7 |
| JSON schemas and project health | Passed release validation |
| WPF startup smoke | Fresh install, existing install with Advanced/Easy mode switching, and Safe Mode passed with coordinated shutdown |
| Full automated release validator | Passed |

The startup smoke initially exposed a missing `BoolToVisibilityConverter` resource in the new Main Window visibility bindings. The resource was registered and the complete three-policy WPF smoke then passed.

The two architecture warnings and nine suggestions are existing composition/test-reference debt tracked under TD-040 through TD-042; this milestone introduces no architecture-validator errors.

## Hardware Validation

Automated simulation covers DirectInput POV values, HID zero/one-based hats, null states, diagonals, separate center press, HOTAS hat-to-mouse flow, PlayStation D-pad-to-Xbox flow, separate same-named hats on multiple devices, profile switching during pointer movement, Emergency Release, and simultaneous Xbox/keyboard/mouse output.

The repository compatibility matrix records real-hardware checks as pending for WinWing Orion 2 stick hats, WinWing Orion 2 throttle hats, a PlayStation controller D-pad, physical mouse output, keyboard output, and formal ViGEm Xbox acceptance. No physical-device pass is claimed without user-run evidence.

## Remaining Limitations

- Strong provider correlation currently requires matching device paths; Container ID support remains the correct next step for safely correlating interfaces whose paths differ.
- Slow and fast pointer modifiers use stable device/control ID fields in Advanced Mode; a guided control picker would improve usability.
- Follow Windows selects the built-in US or Nordic visual geometry from the current UI culture; additional installed keyboard geometries are future data definitions.
- Absolute mouse positioning is not implemented because the current milestone requires reliable relative movement first.
- Vendor-specific continuous POV encodings require an explicit provider adapter and hardware evidence.
- WPF presentation is validated by compilation and startup smoke, not line-coverage instrumentation.
- The project is not Version 1.0 release ready: five known blockers and eight required manual acceptance checks remain open.

## Recommended Next Milestone

The next practical milestone should be hardware acceptance and stabilization, not another broad feature expansion:

1. Run the hat compatibility matrix on the WinWing stick, WinWing throttle, and a PlayStation controller.
2. Validate mouse buttons, wheel, held movement, modifiers, and stop/reset behavior in normal and elevated applications.
3. Validate the visual keyboard against US and Nordic Windows layouts and confirm scan-code behavior in a target game.
4. Add Container ID correlation and a guided slow/fast modifier picker using the collected device evidence.
5. Record latency, memory, reconnect, sleep/resume, and long-duration soak evidence before promoting the milestone toward release readiness.

## Completion

The software portion of the Easy Input and Output Workflow milestone is complete and automatically validated. Existing runtime behavior is preserved, hats use provider-aware normalized state, PlayStation-style D-pads share that path, mouse output is a reset-safe scheduled plugin, Easy Mode remains a presentation layer, and keyboard/mouse/Xbox outputs now have visual mapping and monitoring workflows. Physical hardware acceptance remains explicitly pending.

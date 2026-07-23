# Visual Macro Editor

## Status

The Visual Macro Editor is an Advanced-mode Beta surface controlled by the existing `macro-engine` feature flag. It edits the active profile's existing `MacroDefinition` and `MacroVariableDefinition` objects; the editor does not maintain a second macro format.

## Workflow

1. Open **Macro Editor** in Advanced Mode.
2. Select an existing macro or choose **Add Macro**.
3. Configure the trigger and its friendly device/control selection where required.
4. Add optional conditions.
5. Add and reorder actions, selecting friendly output, mapping, profile, or variable targets.
6. Configure repeat mode, repeat count, and the maximum-iteration safeguard.
7. Resolve the validation summary, enable the macro, and save the profile.

New macros start disabled with a valid Profile Loaded trigger and notification action. This keeps incomplete definitions from producing output.

## Editing Surface

The editor supports:

- create, deep duplicate, confirmed delete, enable, and disable;
- button, axis, encoder, switch, timer, profile, and device triggers;
- the complete existing condition catalog;
- Xbox, keyboard, delay, variable, mapping, profile, and notification actions;
- action ordering;
- one-click clearing of the completed recording preview;
- one-click clearing of every timeline and grouped action while preserving the macro and its action groups;
- Once, Count, Until Released, Until Condition, and Infinite repeat policies;
- Boolean, Integer, and Float runtime-variable definitions;
- immediate validation without hiding invalid definitions.

Stable IDs remain in the profile. WPF selectors display device names, control names, mapping names, profile names, Xbox controls, and keyboard labels.

## Runtime Integration

The view model owns selection and presentation state only. Core owns validation and safe edit operations. The application coordinator remains responsible for configuring `IMacroEngine`.

When mapping is active, profile edits are applied to the macro runtime after a 350 ms debounce. The editor does not read hardware, dispatch OutputActions, call output plugins, or create scheduling loops.

## Persistence And Safety

- **Save Profile** persists macro configuration through the normal profile service.
- Deleting a macro requires confirmation and is not permanent until the profile is saved.
- Clearing actions updates the active profile model immediately but is not permanent until the profile is saved.
- Duplicates receive new macro/action identities and start disabled.
- Runtime variable values, waits, iterations, and held outputs are never persisted.
- Invalid macros remain visible but inactive.
- Mapping stop, profile replacement, emergency reset, Safe Mode, and shutdown retain authority over execution and output cleanup.

## Deferred

Macro libraries, preset packaging, import/export, AI generation, breakpoint semantics, and scripting integration remain separate future work. Network trigger work remains shelved.

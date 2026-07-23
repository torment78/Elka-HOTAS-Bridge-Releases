# Profile Migration

## Supported Upgrade

`ProfileMigration currently upgrades schema v1 through v8 profiles to schema v9. Migration is additive and keeps the original runtime settings intact.

## V1 To V2 Rules

1. Rehydrate missing or null collections and nested curve/settings objects.
2. Add the default shift layer when absent and attach unassigned mappings to it.
3. Add a logical cockpit device group and attach unassigned selected devices.
4. Add the `xbox360` output configuration derived from the legacy output target.
5. Generate standard transform descriptors from existing axis/button processing settings.
6. Preserve unknown extension transform descriptors.
7. Populate application/profile version and migration audit metadata.
8. Set `schemaVersion` to `2`.

## V2 To V3 Rules

1. Add a mapping name derived from input and output when absent.
2. Add a generic output control ID derived from the legacy Xbox enum.
3. Add output kind/configuration, conditions, priority, and notes defaults.
4. Add profile-level Highest Priority conflict policy.
5. Preserve and synchronize existing transform descriptors and legacy runtime fields.
6. Set `schemaVersion` to `3` and record migration audit metadata.

## V3 To V4 Rules

1. Add validated per-device/control calibration with minimum, center, maximum, and offset.
2. Replace generated axis compatibility descriptors with the standard ordered chain: normalization, calibration, deadzone, anti-deadzone, filter, curve, scaling, inversion, and clamp.
3. Preserve unknown extension transforms after the generated chain.
4. Preserve all schema-v4 transform order/settings on normal save; only explicit Axis Curves saves overwrite that mapping's generated compatibility descriptors.
5. Reject analog PWM descriptors on non-axis mappings without deleting the mapping.
6. Set `schemaVersion` to `4` and record migration audit metadata.

## V4 To V5 Rules

1. Rehydrate absent or explicitly null `macros` and `macroVariables` collections.
2. Normalize macro names, descriptions, notes, trigger source strings, action IDs, action configuration, and nested condition collections.
3. Preserve existing mappings, transforms, calibrations, outputs, devices, and profile metadata unchanged.
4. Add no runtime macro state; current variable values, waits, timers, and execution diagnostics are rebuilt at runtime.
5. Set `schemaVersion` to `5` and record migration audit metadata.

### Schema-V9 Additive Macro Playback Fields

Easy macro authoring adds optional `playbackMode`, `actionGroups`, per-action `secondaryNumericValue`, and action `configuration` fields without increasing profile schema v9.

1. Missing playback mode defaults to `Timeline`; existing ordered `actions` execute unchanged.
2. Missing or null action groups/configuration collections are rehydrated safely.
3. Group/action IDs are normalized without changing existing IDs.
4. Sequence cursor, ping-pong direction, recorded key state, and held outputs remain runtime-only and are never migrated or saved.

## V5 To V6 Rules

1. Rehydrate absent or explicitly null `hardware` and `tags` collections.
2. Add optional Category, Difficulty, Game, Aircraft, Vehicle, and Hardware metadata defaults.
3. Trim metadata strings and remove blank or duplicate hardware/tag values case-insensitively.
4. Preserve mappings, macros, transforms, devices, outputs, and runtime-state separation unchanged.
5. Set `schemaVersion` to `6` and record migration audit metadata.

## V6 To V7 Rules

1. Rehydrate absent or explicitly null pointer-output settings with safe defaults and acceleration disabled.
2. Infer cardinal `hatDirection` for legacy Xbox D-pad mappings.
3. Set legacy Xbox D-pad mappings to Combined diagonal behavior so diagonals preserve adjacent cardinal outputs.
4. Keep unrelated hat mappings Dedicated unless they explicitly choose another behavior.
5. Preserve every Xbox/keyboard output ID, key virtual/scan/modifier value, transform, condition, device, macro, output, and unknown extension property supported by the serializer.
6. Keep interface mode, keyboard visual layout, and duplicate-provider override in application settings, not profiles.
7. Set `schemaVersion` to `7` and record migration audit metadata.

## V7 To V8 Rules

1. Create one enabled generated behavior descriptor with stable ID <mappingId>-behavior for every mapping.
2. Project behavior mode, digital inversion, pulse duration, repeat interval, and axis output range from the schema-v7 compatibility fields.
3. Use registered toggle or pulse transform types for those stateful modes; use the registered behavior transform for all remaining modes.
4. Overwrite only generated descriptor IDs during the one-time v7-to-v8 migration so the previously effective legacy behavior wins; preserve unknown/custom descriptors and their settings.
5. Keep generated behavior last when reconstructing pre-v4 chains so preserved extension transforms run before mapping behavior.
6. Preserve schema-v8 descriptor edits on normal load/save. Mapping Editor updates may refresh all generated descriptors for the edited mapping; Axis Curves updates only axis-processing descriptors.
7. Retain behavior, buttonProcessing, pulseDuration, and axisProcessing as compatibility projections for current UI and old files.
8. Set schemaVersion to 8 and record migration audit metadata.

## V8 To V9 Rules

1. Rehydrate and normalize an optional mapping graph only when one already exists.
2. Leave graph-free mappings graph-free so their established ordered transform pipeline is unchanged.
3. Restore missing stable default ports for persisted graph nodes.
4. Preserve transform chains, descriptor authority, outputs, devices, macros, metadata, and unknown supported settings.
5. Store graph behavior in the profile and store node positions/zoom separately in workspace schema v3.
6. Reject invalid graph topology through validation without deleting the mapping.
7. Set schemaVersion to 9 and record migration audit metadata.
## Chapter 9 Compatibility Note

Chapter 9 does not change the profile JSON shape or schema version. Existing `outputPluginId`, `outputControlId`, `outputKind`, and `outputConfiguration` fields already represent Xbox and keyboard targets. Runtime plugin health, held keys, scheduler jobs, PWM phase, rates, and errors are intentionally not migrated or persisted.

## Chapter 10 Compatibility Note

Chapter 10 keeps schema v4. Full PWM settings remain in extensible transform dictionaries, while captured virtual key, scan code, modifiers, and bipolar direction keys remain in `outputConfiguration`. Existing shortcut-only mappings and legacy `frequencyHz` / `fullHoldAtMaximum` settings load unchanged. Runtime key state, phase timers, duty, and pulse counters are never persisted.
## Chapter 11 Compatibility Note

Chapter 11 keeps profile schema v4. Theme choice is stored in application settings, and workspace layouts are stored separately in `Workspaces/workspace-layouts.json`. No profile migration is required, and workspace changes cannot add, remove, or rewrite mappings.

Workspace schema v2 additively stores main-window placement and Devices-grid column widths/order. Existing v1 workspace files receive centered default window bounds and default device columns; profile and runtime configuration remain unchanged.
## Chapter 13 Compatibility Note

Chapter 13 keeps profile schema v4. Recovery-session markers are stored separately in `Recovery/session.json`, and crash reports are stored under `Diagnostics/CrashReports`. Neither document changes profiles, mappings, transforms, selected-device identities, or workspace schemas. Runtime output state is never migrated or restored.

## Chapter 14 Compatibility Note

Chapter 14 keeps profile schema v4. RuntimeSignal recordings are separate versioned documents under `Diagnostics/SignalRecordings`, and Test Runner exports are stored under `Diagnostics/TestRuns`. Playback never restores runtime output state, and neither document changes profiles, mappings, transforms, devices, settings, or workspaces.

## Chapter 15 Compatibility Note

Chapter 15 keeps profile schema v4. Plugin manifests live separately under the application-data Plugins directory and catalog lifecycle/compatibility state is runtime diagnostics only. Existing `xbox360` and `keyboard` routing IDs do not change, and external manifests cannot rewrite or delete profile mappings.

## Chapter 16 Compatibility Note

Chapter 16 keeps profile schema v4 and advances application settings additively from v1 to v2. Existing settings without `allowExperimentalFeatures` or `featureFlagOverrides` load with the developer opt-in disabled and catalog defaults. Unknown feature keys are retained but ignored. Disabling a feature never deletes mappings, transforms, presets, recordings, or plugin manifests; composition changes apply after restart.

## Chapter 22 Compatibility Note

Chapter 22 keeps profile schema v6 and advances application settings additively from v2 to v3. Existing settings load with First Run Setup incomplete, Stable update channel, no last-update timestamp, and no behavior change to profiles or mappings. Existing settings/profile/workspace files are detected before initialization, so upgraded installations skip automatic setup.

The deployment backup is separate from profile migration backup. It can contain settings, profiles, workspaces, and previous application files. Neither backup contains runtime output, timers, key state, macro execution, signal history, or scheduler state.

Driver status and update-check state are application concerns and never modify profile schema v6.

## Application Settings V5 To V6

Application settings schema v6 adds `logRetentionDays`. Existing files receive the 14-day default; loaded and saved values are clamped to 1-365 days. This settings migration does not change profile schema v9, workspace layouts, mappings, outputs, or runtime state.

## Backup Rule

When `JsonProfileStore` loads an older active profile, it copies the exact pre-migration JSON to `Backups` before writing the migrated document. Backup names include the profile file name, source schema, and UTC timestamp. Import never alters the source file. A new import requires no backup; replacing an active profile creates a separate timestamped `before-import` backup before the replacement is saved.

## Failure And Newer Schemas

- A malformed JSON document fails loading with its file path in the exception context.
- Invalid mappings do not stop a supported migration from being persisted; the Profile Health Report exposes them afterward so legacy user data is not discarded.
- A schema newer than the current application is left unchanged, reported as an error, and blocked from save to prevent accidental downgrade.
- Migration never deletes mappings for disconnected or missing devices.

## Migration Status

Profiles expose `lastMigratedFromSchemaVersion` and `lastMigrationUtc`. The Profile Health Report displays either current, migrated, migration-required, or unsupported-newer status.

## Adding A Future Migration

For schema v10 or later:

1. Add a version-specific migration step instead of replacing v1/v2 logic.
2. Keep each step deterministic and safe to run once.
3. Update the JSON Schema and version history.
4. Add historical JSON fixtures and preservation assertions.
5. Verify an exact backup is made before the active file changes.
6. Verify unknown extension data remains intact where forward compatibility allows.
7. Update `PROFILE_SYSTEM.md`, `PROFILE_SCHEMA.md`, the changelog, and the completion report.

## User Impact

No manual migration is required for schema v1-v7 users. The application upgrades supported profiles on load and stores the original in the separate backup directory.

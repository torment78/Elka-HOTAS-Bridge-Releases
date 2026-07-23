# Profile System

Chapter 6 introduces a versioned profile-management layer while preserving the existing HOTAS mapping runtime.

## Responsibilities

Profiles contain configuration only:

- identity and descriptive metadata;
- logical device groups and device matching hints;
- input mappings and transform configuration;
- output plugin configuration;
- axis curves and shift layers.

Live signal values, toggle state, timers, scheduler state, and output state remain in runtime services and are never serialized.

## Storage Layout

The default root is `%LOCALAPPDATA%\HOTASBridge`. `HOTASBRIDGE_DATA_ROOT` may override it for isolated development and UI tests.

| Content | Location |
| --- | --- |
| User profiles | `Profiles\*.json` |
| Migration backups | `Backups\*.json` |
| Application settings | `Settings\application-settings.json` |
| Default templates | Built-in catalog; future external templates use `Templates` |
| Logs | `Logs` |
| Diagnostics | `Diagnostics` |
| Temporary atomic writes | `Temp` |

## Profile Lifecycle

1. `JsonProfileStore` reads JSON with camel-case properties and string enums.
2. `ProfileMigration` rehydrates optional collections and upgrades supported older schemas.
3. The exact source file is copied to `Backups` before an on-disk migration is written.
4. `ProfileValidator` generates a health report.
5. Warnings remain informational; critical errors block a normal save.
6. Saves use a temporary file followed by an atomic replacement in the same data root.
7. Profile persistence publishes a typed Saved event after the write is accepted, or SaveSkipped when change-aware Auto Save finds no changes.

Import creates a new profile identity when the incoming ID already exists. Duplicate and Save As always create a new profile identity and reset its revision before the first save. Rename preserves identity. Export does not add a profile to the active store. `IProfileManagementCoordinator` sequences these CRUD operations without depending on WPF collections or dialogs. Deleting the last profile saves an empty replacement first; a failed original delete removes that replacement and preserves tracking for the original profile.

## Device Groups And Matching

A profile can group several independent devices into one logical cockpit. Each selected device stores a group ID plus identity evidence. Reconnection matching uses the strongest available evidence in this order:

1. stable internal device ID/GUID;
2. Container ID;
3. serial number;
4. device path;
5. vendor/product identity and HID usages;
6. friendly name.

Windows HID discovery supplies a validated SetupAPI Container ID when available. Reconciliation backfills this additive field into existing loaded selections without changing mapping references, and the next normal save persists it.

An unmatched device is marked missing. Its mappings are preserved.

## Target Application Activation

`targetApplication` is optional profile metadata. Leaving it empty disables automatic activation for that profile. Users can enter or browse to an executable, or select a currently running local process from the Profiles page.

The process monitor is UI-independent and uses only local process ID, process name, start time, and executable path when Windows permits access. It does not read command lines, contact a network service, or start applications. Matching follows these rules:

1. an exact normalized executable path is strongest;
2. a file-name/process-name match is used when the profile stores only a name;
3. a newly observed process activates at most one matching profile;
4. equal-strength matches are reported as a conflict and no profile is selected;
5. Safe Mode disables automatic activation.

Activation changes the active profile and refreshes its selected input source. It does not start mapping, create output devices, or restore runtime output state. See [PROCESS_AWARE_PROFILES.md](PROCESS_AWARE_PROFILES.md).

## Mapping And Transform Storage

Every mapping has a stable mapping ID, source device/control, output plugin/control, enabled state, shift layer, and an ordered transform chain. Transform nodes have their own IDs, type names, enabled state, and string-valued settings dictionary.

Schema v9 retains axisProcessing, buttonProcessing, behavior, and pulseDuration as compatibility projections for old files and current authoring controls. Enabled generated behavior descriptors are the runtime authority. Normal persistence preserves descriptor edits; explicit Mapping Editor updates refresh all generated descriptors for that mapping, while Axis Curves refreshes only axis-processing descriptors. Unknown extension transforms remain untouched. Schema v9 also stores an optional versioned mapping graph. Graph-free mappings keep the linear transform chain; branching graphs reference the same transform configurations and are never flattened during save, comparison, merge, or migration. Canvas positions and zoom remain workspace data.

## Output Configuration

`outputs` stores plugin ID, display name, enabled state, and plugin configuration. Runtime controller state is excluded. The current built-in plugin ID is `xbox360`; unavailable plugins are reported as errors and disabled plugins as warnings.

## Validation And Health

The Profile Health Report is recalculated when a profile opens, devices refresh, mappings change, or a save is attempted. It reports:

- missing devices;
- duplicate device, group, or mapping IDs;
- incomplete or duplicate mappings;
- conflicting output assignments;
- unavailable or disabled output plugins;
- invalid axis ranges and transform descriptors;
- deprecated or unsupported schema versions;
- migration status.

Status is `Healthy`, `Warnings`, or `Errors`. Selecting an issue navigates to Profiles, Connected Devices, Mappings, Axis Curves, Xbox Output, or Settings as appropriate. The report never modifies a profile.

## Save Modes And Settings

- Manual Save updates the current profile.
- Save As creates and selects an independent copy.
- Auto Save uses a configurable 15-3600 second interval and persists only when profile content changed.
- Import, Export, Duplicate, and Rename are available from the Profiles page.
- The ten most recently opened persisted profiles are stored in application settings.

`IProfilePersistenceCoordinator` records an accepted content fingerprint after load or successful persistence. The fingerprint includes nested profile configuration, so edits to mappings, transforms, macros, devices, outputs, and metadata are detected without UI-specific dirty flags. Manual Save remains an explicit forced revision. `ProfileCommandsViewModel` owns command presentation, while `IProfileDialogService` keeps file and confirmation windows behind an App-layer interface.

## Default Templates

Built-in templates are separate from user profiles:

- Empty Profile;
- Generic HOTAS;
- Xbox Mapping Starter.

Creating from a template returns a new profile ID and does not mutate the template.

## Extension Rules

- Add fields and transform types additively where practical.
- Preserve unknown transform descriptors during migration.
- Never serialize runtime state into a profile.
- Never silently delete missing-device mappings.
- Increment `SchemaVersion` only for structural compatibility changes.
- Add a migration, backup behavior, schema update, and regression fixture for every schema increment.

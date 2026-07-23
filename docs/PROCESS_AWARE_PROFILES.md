# Process-Aware Profiles

## Purpose

Process-aware activation is an optional local convenience. A saved profile may name the Windows executable it belongs to. When HOTASBridge observes that application starting, it can select the profile without changing mappings or starting outputs.

## Boundaries

- `IRunningApplicationCatalog` is the Core abstraction for read-only process snapshots.
- `SystemRunningApplicationCatalog` is the Windows implementation in Infrastructure.
- `ProfileProcessActivationPolicy` owns deterministic matching and newly observed process state.
- `MainViewModel` samples the catalog every two seconds and applies an activation decision on the WPF dispatcher.
- The Mapping Engine, input providers, and output plugins do not inspect Windows processes.

The catalog reads process ID, process name, start time, and executable path when Windows permits it. It does not inspect command-line arguments, launch applications, access networks, or persist process history.

## Matching Rules

1. Empty `targetApplication` metadata disables activation for that profile.
2. A normalized full executable path is an exact match and has the highest strength.
3. A stored executable name can match the local process name or executable file name.
4. Only newly observed process identities are evaluated, so a running application does not repeatedly force a profile selection.
5. One strongest profile is activated.
6. Multiple strongest matches produce a conflict warning and no profile change.
7. Safe Mode suppresses all automatic profile activation.

## User Workflow

On **Profiles**, select a profile and either browse to an EXE or refresh the local process list and use the selected process. Save the profile to persist the target. Choose **Clear** to disable automatic activation.

The running-application picker is populated only by the explicit **Refresh processes** command. Automatic activation continues to use background snapshots without rebuilding the picker, preserving profile-page selection and scroll position.

Activation selects the saved profile, applies its input-source preference, refreshes devices where necessary, records it as the last active profile, and updates diagnostics. It does not start mapping or create the virtual Xbox controller.

## Failure Handling

Individual inaccessible or exited Windows processes are skipped. Catalog failures are logged and shown as a non-blocking status. Ambiguous matches remain visible as warnings and never choose a profile arbitrarily.

## Validation

Core tests cover initial/new process detection, no-repeat behavior, exact-path precedence, conflicting profiles, PID reuse, path mismatch, and deterministic newest-process selection. An integration test verifies that the Windows catalog excludes HOTASBridge's own process and returns unique application identities.

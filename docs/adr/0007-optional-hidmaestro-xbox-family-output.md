# ADR 0007: Add HIDMaestro as an Optional Xbox-Family Output Backend

- Status: Accepted
- Date: 2026-08-05
- Decision owners: HOTASBridge maintainers

## Context

HOTASBridge already provides a working virtual Xbox 360 controller through ViGEmBus. Some games and diagnostics distinguish that device identity from an Xbox One controller. Replacing ViGEm would regress existing installations and profiles, while changing mapping output IDs would create unnecessary migration and duplicate authoring paths.

HIDMaestro 1.4.3 provides a user-mode virtual HID stack and data-driven built-in profiles for Xbox 360, One, One S, Series X|S, Elite, and Adaptive identities. It has a different driver lifecycle and installs a locally trusted certificate, so enabling or installing it must be deliberate and diagnosable.

## Decision

Retain ViGEm Xbox 360 as the stable, enabled-by-default backend. Add HIDMaestro as a Beta feature that is disabled by default and composed only after a restart when the user enables `xbox-one-output`.

Both backends implement the existing `IVirtualGamepadOutput` contract and use separately identified `XboxOutputPlugin` instances. Existing mappings remain logical Xbox mappings with plugin ID `xbox360`. Output Manager routes those actions to whichever Xbox backend the active profile selects. Only one Xbox backend may be enabled for a profile. The inactive backend is disabled in the UI and Output Manager independently normalizes invalid dual-enabled profile input. A profile-owned setting selects a curated HIDMaestro Xbox identity only while output is stopped.

HIDMaestro driver installation is an explicit Output Monitor action with confirmation and Windows elevation. Normal startup, profile loading, and Start Mapping never install the driver. The SDK binary, license, dependency notices, version, and release hash are kept with the source; no signing certificate or private key is stored.

## Alternatives

- Replace ViGEm with HIDMaestro. Rejected because it would change a working compatibility baseline and force all users onto a Beta driver path.
- Store `xboxone` on every Xbox mapping. Rejected because output identity is a profile-level backend choice, not a different control model, and existing profiles should not migrate.
- Build a new virtual HID driver. Rejected because driver development, signing, maintenance, and security review are outside the current product scope.
- Install HIDMaestro from the application installer. Rejected for this milestone because optional driver installation must remain clearly separated, user-confirmed, and easy to diagnose.

## Consequences

- Existing Xbox mappings and profiles remain compatible.
- Users can select a supported Xbox-family device identity without losing the stable ViGEm Xbox 360 path.
- The optional SDK increases packaged application size but is not loaded during normal default operation.
- Driver health, setup, plugin lifecycle, output state, and errors remain visible through existing diagnostics.
- Release acceptance requires clean-machine driver installation and physical game validation before the backend can be promoted from Beta.

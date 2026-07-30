# ADR 0006: Defer vJoy Output And Driver Bundling

- Status: Accepted
- Date: 2026-07-29
- Decision owners: HOTASBridge architecture owner

## Context

HOTASBridge already discovers user-installed vJoy devices as virtual inputs. Generated Xbox output uses the existing ViGEm-backed plugin, while keyboard and mouse outputs use Windows `SendInput`.

Adding vJoy output would add another output plugin, native SDK boundary, kernel driver, installation path, health model, licensing obligation, and clean-machine acceptance matrix. The original vJoy repository states support only through Windows 10 1803 and redirects newer Windows users to forks. The `jshafer817/vJoy` release page describes version 2.1.9.1 as attestation-signed for Windows 10 and describes Windows 11 operation only as a report rather than a supported guarantee:

- https://github.com/shauleiz/vJoy
- https://github.com/jshafer817/vJoy
- https://github.com/jshafer817/vJoy/releases

That evidence is not strong enough to bundle a second kernel driver in the current release.

## Decision

The current release will:

- continue recognizing an already installed vJoy device as input;
- not create a vJoy output device;
- not bundle, install, update, repair, or remove a vJoy driver;
- keep ViGEmBus as the only bundled virtual-controller dependency.

A future vJoy output requires an explicit use case, selection of a maintained signed upstream, license/provenance review, and supported-Windows acceptance. Its implementation must be an independent `IOutputPlugin` consuming `OutputAction`, with idempotent reset, isolated failure handling, health diagnostics, and explicit user-approved driver setup.

## Alternatives

- Bundle vJoy immediately. Rejected because upstream maintenance and Windows 11 support evidence are not strong enough for a release driver dependency.
- Reuse vJoy native APIs directly from the Mapping Engine. Rejected because it violates the Output Manager and plugin boundaries.
- Remove vJoy input recognition. Rejected because existing user-installed preprocessing paths remain valid and are already supported by the input model.

## Consequences

- Current Xbox, keyboard, and mouse behavior and installer contents remain unchanged.
- Users can continue feeding HOTASBridge from vJoy when they install and configure it separately.
- vJoy output remains visible as deferred scope instead of being mistaken for missing current-release functionality.
- Any later vJoy implementation has an explicit driver, architecture, diagnostics, licensing, and acceptance checklist.

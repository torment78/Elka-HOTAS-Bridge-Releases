# TrackIR Integration

## Scope

HOTASBridge includes a provider for the NaturalPoint TrackIR Game Client API. It is additive to the existing OpenTrack and LookPilot providers and publishes the same immutable `HeadPose` contract.

The implementation is a managed native-interop adapter. The repository and installer do not contain NaturalPoint SDK headers, sample code, libraries, or the downloaded SDK ZIP.

## User Requirements

1. Install and start the official TrackIR software.
2. Connect and verify the TrackIR camera in that software.
3. Disable TrackIR mouse emulation.
4. Close any other game or client currently consuming TrackIR.
5. In HOTASBridge Head Tracking, select **TrackIR** and enable head tracking.

HOTASBridge locates the client library from:

```text
HKCU\Software\NaturalPoint\NATURALPOINT\NPClient Location
```

A downloaded SDK ZIP alone is not the runtime dependency. The installed TrackIR software supplies `NPClient64.dll` or `NPClient.dll`.

## Runtime Contract

The provider follows the SDK lifecycle:

1. Load the architecture-matching NPClient library.
2. Verify the DLL and application signatures.
3. Register the HOTASBridge top-level window.
4. Query the client version.
5. Register public SDK profile ID `1000`.
6. Request Roll, Pitch, Yaw, X, Y, and Z.
7. Start data transmission.
8. Poll changed frame signatures every 8 ms.
9. Stop transmission and unregister on shutdown.

`TRACKIRDATA` is marshaled as a one-byte-packed 68-byte structure. Rotations map from TrackIR units to degrees and translations map to centimeters. The provider converts TrackIR's left-handed horizontal/depth directions to the common HOTASBridge pose convention.

No new frame for 300 ms publishes one inactive pose and changes provider health to Tracking Lost. Invalid values and native errors are contained at the provider boundary. TrackIR mouse-emulation state is reported as a conflict instead of producing duplicate cursor output.

## Licensing And Distribution

The integration was implemented against the official TrackIR SDK package and public developer documentation: <https://www.trackir.com/developers>.

Before a public TrackIR-enabled release:

- review the current NaturalPoint SDK EULA;
- add the required NaturalPoint protections/disclaimers to the end-customer product EULA;
- confirm whether NaturalPoint requests registration or a product-specific developer ID;
- verify the installer contains no NaturalPoint SDK samples, headers, or client DLLs;
- record the review and physical acceptance evidence under `TD-048`.

This document records an engineering release gate, not legal advice.

## Validation

Automated tests cover catalog availability, native structure sizes, all-six-axis conversion/sign normalization, non-finite rejection, changed-frame publication, stale timeout, mouse-emulation conflict behavior, and native-client lifecycle through a fake client.

Manual acceptance requires a physical TrackIR camera and official software. Record software/firmware/Windows versions, smooth six-axis motion, activation/recenter, tracking loss/recovery, single-client conflict, mouse-emulation conflict, sleep/resume, profile reload, and clean shutdown in `docs/HARDWARE_COMPATIBILITY.md`.

# TD-028 Windows Container IDs Completion Report

Status: Complete

## Existing Implementation Assessment

| Requirement | Before TD-028 | Result |
| --- | --- | --- |
| Container field in runtime identity | Already implemented | Preserved `DeviceIdentity.ContainerId`. |
| Container field in profile JSON | Already implemented | Preserved nullable `ProfileDeviceSelection.ContainerId`; no schema change. |
| Matching priority | Already implemented | Preserved Container ID ahead of serial number and device path. |
| Native Windows retrieval | Missing | Added SetupAPI property retrieval during existing HID interface enumeration. |
| Existing-profile enrichment | Partial | Same-Stable-ID reconciliation now backfills newly available identity metadata. |
| Stable ID compatibility | Required | Stable ID construction is unchanged. |

## Implementation

- `WindowsHidDeviceProvider` now asks `SetupDiGetDeviceInterfaceDetailW` for both the symbolic path and its `SP_DEVINFO_DATA` record.
- The provider reads `DEVPKEY_Device_ContainerId` with `SetupDiGetDevicePropertyW`.
- Only a `DEVPROP_TYPE_GUID` value containing a non-empty 16-byte GUID is accepted.
- Missing, malformed, or unavailable native properties produce a null Container ID and do not interrupt discovery.
- `DeviceIdentityMatcher.Reconcile` now refreshes profile identity metadata even when the Stable ID is unchanged.
- A known Container ID is retained when a later same-device discovery temporarily cannot read the property.
- Mapping, curve, and shift-layer references are rewritten only when the Stable ID actually changes.
- Existing profile JSON remains valid and the normal save path persists backfilled metadata.

## Compatibility

- No profile schema version change.
- No Stable ID algorithm change.
- No mapping format change.
- No direct hardware access outside the Input assembly.
- Existing serial, path, VID/PID, usage, and friendly-name fallbacks remain active.
- DirectInput remains deferred pending evidence that HID cannot expose required devices or semantics.

## Tests

- Valid GUID Container properties parse to canonical `D` format.
- Wrong property types, short buffers, and `Guid.Empty` are rejected.
- Direct Windows HID enumeration requires valid Container IDs for every controller it returns.
- Same-Stable-ID reconciliation backfills Container ID without changing mapping references.
- A transient null lookup does not erase a known same-device Container ID.
- Profile JSON round-trips Container ID metadata.

## Validation

- Debug build: passed with 0 warnings and 0 errors.
- Automated tests: 360 passed, 0 failed, 0 skipped.
- Merged runtime line coverage: 58.52% (`15,992/27,329`).
- Release build: passed with 0 warnings and 0 errors.
- Architecture validator: 0 errors, 0 warnings, 0 suggestions.
- Direct Windows HID boundary tests: passed, including valid Container IDs for every enumerated controller.
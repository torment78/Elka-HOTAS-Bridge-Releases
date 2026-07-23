# TD-034 Local Profile Packages Completion Report

## Scope

This milestone completes the evidence-backed local portion of TD-034:

- signed HOTASBridge profile packages;
- offline publisher trust and provenance;
- bounded package screenshots with import preview;
- one conservative vendor converter for the officially documented basic Thrustmaster T.A.R.G.E.T. `MapKey` grammar.

Online/community/cloud repositories remain explicitly excluded. WinWing SimAppPro, Logitech, Virpil, and VKB converters remain evidence-gated because no stable public interchange schema or approved fixture set is available in the repository.

## Existing Implementation Assessment

| Requirement | Initial state | Result |
| --- | --- | --- |
| Importer/exporter registry | Implemented | Reused without a second profile model |
| Legacy JSON profiles/templates | Implemented | Preserved unchanged as an explicit legacy format |
| Compatibility preview and replacement backup | Implemented | Extended with package-security evidence |
| Signed packages and provenance | Missing | Added `.hotasbundle`, ECDSA P-256/SHA-256, fingerprints, and trust status |
| Screenshot package media | Missing | Added one bounded PNG/JPEG asset and decode-limited preview |
| Vendor conversion | Missing | Added conservative basic T.A.R.G.E.T. `MapKey` conversion |
| Online/community services | Deferred | Explicitly excluded from the approved local backlog |

## Implementation

### Signed Bundle

`SignedProfilePackageExporter` creates a bounded ZIP archive containing top-level manifest, profile, signature, and optional screenshot entries. The signature covers a domain-separated hash of the exact manifest, profile, and screenshot bytes. ZIP timestamps are stable, entries are never extracted, and unreferenced or path-bearing entries are rejected.

`SignedProfilePackageImporter` validates archive shape, resource limits, image metadata, ECDSA key size, public-key fingerprint, payload signature, and local publisher trust. Import preview reports:

- valid and trusted;
- valid but unfamiliar publisher;
- missing signature;
- invalid signature.

Missing or invalid signatures block import. A valid unfamiliar publisher remains importable only through the existing user-reviewed compatibility preview.

### Local Publisher Identity

`WindowsProfilePackageSigningIdentity` stores the non-exportable private key in the Windows current-user CNG key store. The app data directory stores only publisher ID, display name, key name, and public-key fingerprint. `JsonProfilePackageTrustStore` loads an offline explicit allow-list and always includes the active local publisher.

Key-store failure does not block application startup. HOTASBridge logs the failure and uses an in-memory publisher for that session; such packages remain cryptographically verifiable but appear unfamiliar after restart.

### Screenshots

The Profiles page can attach one PNG or JPEG to a signed export. Import preview shows a decode-limited thumbnail only after cryptographic signature validation. Package media is limited to 5 MiB, 8192 pixels per dimension, and 24 million pixels. Screenshot content does not enter `HotasProfile`, profile schema, or runtime state.

### Thrustmaster T.A.R.G.E.T.

The `.tmc` importer follows the basic `MapKey(&Device, Control, event);` form documented in the official T.A.R.G.E.T. Script Editor Basics manual. It converts simple quoted or named single keys to disabled Keyboard Output mappings and retains symbolic device/control names for rematching.

Advanced expressions, DirectX events, axis functions, macros, timing, layers, modifiers, and `CHAIN` are not guessed. Unsupported statements are line-numbered conversion warnings. This is intentionally partial and never presented as complete conversion.

Reference: [Thrustmaster T.A.R.G.E.T. Script Editor Basics v1.5](https://ts.thrustmaster.com/download/accessories/pc/hotas/software/TARGET/TARGET_Script_Editor_Basics_v1.5_ENG.pdf).

## Automated Validation

- 401 tests passed, 0 failed, 0 skipped.
- Merged runtime coverage: 58.27% (`18,636/31,982`).
- Debug and Release builds: 0 warnings, 0 errors.
- Architecture validator: 0 errors, 0 warnings, 0 suggestions.
- Architecture review schedule: 3 satisfied gates, 3 planned gates.
- Isolated WPF smoke: fresh/existing Easy and Advanced, signed Profiles package UI, Node Editor, Script Workbench, Safe Mode, and coordinated shutdown passed.
- Focused regressions cover trusted round-trip, screenshot media, unfamiliar publishers, tampering, TARGET partial conversion, offline trust loading, ephemeral signing, and CNG identity persistence.

## Deferred And Excluded

- WinWing SimAppPro, Logitech, Virpil, and VKB conversion requires official schemas or representative user-approved fixtures and deterministic tests (TD-046).
- Online/community/cloud profile services, ratings, Steam Workshop, and repository synchronization are excluded.
- Automatic plugin/dependency acquisition is excluded.
- Application/installer release signing remains TD-035 and is separate from profile-package signing.

## Result

TD-034 is complete for the approved evidence-backed local scope. Existing JSON profiles remain compatible, profile runtime behavior is unchanged, and unsupported vendor formats remain explicit rather than silently misconverted.
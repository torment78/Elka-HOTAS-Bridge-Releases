# ADR 0003: Normalize Hats and Keep Generated Outputs Behind Plugins

- Status: Accepted
- Date: 2026-07-18
- Decision owners: HOTASBridge architecture owner

## Context

Hat and D-pad providers report incompatible encodings: DirectInput hundredths, HID zero- or one-based logical ranges, null values, and individual direction buttons. Treating every raw number as one global format caused intermittent center states and incorrect mappings. The milestone also adds mouse output and simpler visual mapping without creating a second runtime or profile type.

## Decision

Normalize provider-described hat input into immutable `HatState` metadata while preserving the raw value. Center idle and a physical center press remain distinct. Mapping-owned diagonal policy adapts the normalized signal immediately before the existing mapping pipeline.

Mouse output is an internal output plugin consuming ordinary `OutputAction` values and sharing the centralized scheduler. Easy Mode, visual selectors, and presets are presentation over the same profile-owned `InputMapping` model. Profile schema v7 adds only mapping-owned hat and pointer configuration. Application presentation mode and keyboard visual layout remain application settings.

Provider correlation prefers the strongest live/control-enumerating representation of the same device interface, warns about likely duplicates, preserves distinct processed virtual devices, and provides an explicit Advanced override.

## Alternatives

- Interpret every hat as DirectInput hundredths. Rejected because HID logical ranges and button-backed D-pads are different contracts.
- Add device-specific PlayStation mapping code. Rejected because its D-pad works through the normalized provider-described hat path.
- Generate mouse input inside input or mapping code. Rejected because lifecycle, reset, safety, scheduling, and diagnostics belong to an output plugin.
- Create separate Easy Mode profiles or mappings. Rejected because mode switching must never alter runtime behavior or user data.

## Consequences

- Input providers must publish accurate hat descriptors where known; provider-less legacy conversion is explicitly compatibility-only.
- Existing Xbox and keyboard mappings migrate without replacement, while old Xbox D-pad mappings gain cardinal direction metadata and combined diagonals.
- Mouse movement, wheel state, and held buttons participate in plugin reset and Emergency Release.
- New providers can add encodings through metadata without changing the Mapping Engine.
- Physical WinWing and PlayStation validation remains a release-evidence task; automated simulated encodings protect the runtime meanwhile.

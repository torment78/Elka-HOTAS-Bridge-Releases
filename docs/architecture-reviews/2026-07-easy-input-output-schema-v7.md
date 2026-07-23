# Architecture Review: Easy Input/Output and Profile Schema v7

- Date: 2026-07-18
- Gate ID: `profile-schema-change`
- Scope: Easy/Advanced presentation, provider-aware hat normalization, provider correlation, mouse output, visual keyboard/mouse selection, and additive profile schema v7
- Decision owners: HOTASBridge architecture owner
- Reviewers: HOTASBridge engineering review
- Outcome: Accepted with manual hardware follow-up
- Related ADRs: [ADR 0003](../adr/0003-normalized-hat-and-output-plugins.md)
- Related issues/debt: Physical WinWing and PlayStation compatibility evidence remains pending

## Executive Summary

The milestone preserves the RuntimeSignal, Mapping Engine, Output Manager, scheduler, and profile foundations. Hat encoding is normalized at the provider boundary, mouse is a normal output plugin, and Easy Mode is presentation only. Schema v7 is accepted as an additive mapping-configuration change with automatic backup migration from v1-v6.

## Evidence

- Debug solution build completed with zero warnings and zero errors during implementation.
- 251 automated tests pass across Core, Integration, Architecture Validator, and Scripting suites before final release validation.
- Tests cover DirectInput and HID hat encodings, null/center states, diagonals, center press, PlayStation-style D-pad, presets, mouse scheduling/reset, provider correlation, keyboard layout identity, and v6-to-v7 preservation.
- Existing Xbox output remains wrapped by `XboxOutputPlugin`; no ViGEm backend replacement occurred.
- Profile migration still writes an exact pre-migration backup before replacing an active profile.

## Performance

Hat normalization is a small deterministic conversion performed when reports arrive. Repeated identical HID directions are suppressed by the live provider. Mouse movement uses one shared scheduler job for all active pointer mappings, not one thread per mapping. UI visuals continue to sample runtime diagnostics independently from output frequency.

## Maintainability

`HatDescriptor`, `HatState`, `HatNormalizer`, and `HatMappingSignalAdapter` separate provider parsing from mapping policy. `MouseOutputPlugin` owns SendInput, scheduling, state, diagnostics, and cleanup. `EasyMappingPresetCatalog` creates ordinary mappings and is unit tested independently from WPF. The existing MainViewModel remains a coordination hotspot and should continue to be split incrementally.

## Public APIs

No supported external Plugin API or Script API version changes. New Core domain records are internal application contracts, not a new third-party compatibility promise. The profile JSON contract advances from v6 to v7 and receives a machine-readable schema.

## Plugin Interfaces

Mouse uses the existing `IOutputPlugin` lifecycle and `OutputManager` isolation. Xbox, keyboard, and mouse can run concurrently and all respond to shared reset/emergency release. No input implementation or WPF class is exposed to output plugins.

## Runtime Diagnostics

Device Inspector receives normalized direction, raw value, provider, direction count, centered/active state, center-button state, last change, and mapping count from cached signals. Output Monitor consumes only manager snapshots for Xbox, keyboard, and mouse state. Provider-correlation warnings and counts feed diagnostics without altering profiles.

## Backward Compatibility

Schemas v1-v6 migrate to v7. Existing Xbox and keyboard IDs/configuration remain unchanged. Older Xbox D-pad mappings infer their cardinal direction and use Combined diagonal behavior; unrelated mappings retain defaults. Null pointer settings are rehydrated. Easy/Advanced mode and keyboard visual layout are settings, not profile behavior. Existing pre-mode users migrate to Advanced so navigation does not unexpectedly disappear.

## Findings

| ID | Severity | Finding | Resolution or owner |
| --- | --- | --- | --- |
| F-101 | Decision | Provider metadata, not one global numeric table, determines hat decoding. | ADR 0003 |
| F-102 | Decision | Mouse remains an isolated output plugin on the centralized scheduler. | ADR 0003 |
| F-103 | Decision | Easy Mode and presets use the same profile and mapping model. | ADR 0003 |
| F-104 | Warning | Physical WinWing and PlayStation reports have not been exercised in this automated environment. | Manual hardware matrix |
| F-105 | Advisory | Slow/fast mouse modifiers currently use stable control IDs in the Advanced editor; a future picker can improve ergonomics. | Next practical UX milestone |
| F-106 | Advisory | DirectInput live input remains a future provider; its encoding is covered at the normalization contract. | Input roadmap |

## Decision

Accepted with manual hardware follow-up. Profile schema v7 is approved. Preserve the normalized provider boundary, plugin-owned outputs, one mapping model, and non-destructive migrations.

## Follow-Up

- Record WinWing Orion stick/throttle and physical PlayStation results in `docs/HAT-COMPATIBILITY.md` when hardware is available.
- Run the final Debug/Release/test/architecture/smoke validation and record exact results in the milestone completion report.
- Improve modifier selection from free-form IDs to device/control pickers in a later focused UI iteration.

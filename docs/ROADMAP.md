# HOTASBridge Roadmap

## Product Direction

HOTASBridge evolves incrementally from a hardware-validated HOTAS-to-Xbox bridge into a general Windows input signal-processing platform. Working input, mappings, profiles, Xbox output, keyboard output, and driver integration remain protected throughout that evolution.

The current product line is `0.x`. Version `1.0` is reserved for the first supported public release.

## Current Baseline

The architecture milestones completed through Chapter 25 provide:

- standardized immutable RuntimeSignals and an engine-owned latest-value cache;
- provider-based HID, Raw Input, and simulation discovery;
- indexed mapping and modular transform execution;
- independent Xbox and keyboard output plugins with centralized scheduling;
- versioned profiles, migration, validation, and health reporting;
- Device, Mapping, Output, Signal Flow, reliability, and performance diagnostics;
- recording/playback architecture and a hardware-independent simulation provider;
- plugin manifests, compatibility validation, and offline extension points;
- recovery, Safe Mode, crash reports, and a Runtime Watchdog;
- centrally versioned builds and a UI-independent feature policy;
- consolidated Project Health, measured coverage, and repeatable final validation;
- a Beta Visual Node Editor with optional schema-v9 branching graphs over the same profile-owned mapping model;
- a Beta Macro Engine and Visual Macro Editor with centralized scheduling, runtime variables, validation, and diagnostics;
- an Experimental Lua runtime behind an independently versioned public Script API, constrained host surface, and central scheduler;
- a compilable Inno Setup deployment foundation, versioned backup/restore services, shared driver detection, and a non-blocking First Run Wizard;
- executable project-boundary validation, contributor/code-style guidance, ADR governance, and release-gate integration;
- a Beta, offline, read-only AI Explain advisor grounded in current configuration and shared runtime diagnostics;
- a living product vision, formal governance model, accepted foundation ADRs, and executable architecture-review schedule.

## Delivery Phases

| Phase | Objective | Status | Exit evidence |
| --- | --- | --- | --- |
| 1. Architecture Stabilization | Boundaries, DI, documentation, regression protection | Complete | Core abstractions and automated baseline tests |
| 2. Diagnostics | Inspect devices, mappings, outputs, signal flow, and runtime health | Complete for Version 2 | Cache-backed diagnostic pages and telemetry |
| 3. Mapping Improvements | Transform chains, Learn Mode, presets, profile health | Complete for Version 2 | Validated live editing and persisted configuration |
| 4. Keyboard Output | SendInput, capture, PWM, scheduler integration | Complete for Version 2 | Tracked cleanup and integration tests |
| 5. Performance | Central scheduling, queue metrics, profiling, allocation reduction | Complete for Version 2 | Runtime metrics and performance profiler |
| 6. Advanced Features | Layers, conditions, recording, playback, variables, macros | Complete foundation | Layers, conditions, recording/playback, runtime variables, macros, and diagnostics exist behind stable/Beta boundaries |
| 7. Public Release | Installer, migration rehearsal, hardware matrix, beta, support docs | In progress | Signed release artifacts exist; clean-machine validation, migration rehearsal, hardware acceptance, soak evidence, and the Version 1 review remain |

## Near-Term Milestones

### 0.17 - Release Preparation Foundation

- add consolidated release-readiness and acceptance evidence;
- automate restore, build, test, coverage, documentation, schema, and artifact checks;
- complete user/developer/release documentation foundations;
- define the supported Windows and production .NET runtime matrix;
- produce an installer that handles the optional ViGEmBus prerequisite transparently and safely;
- add upgrade/uninstall validation on clean Windows environments;
- establish signed artifact and checksum generation;

### 0.18 - Hardware and Profile Beta

- validate the branching Visual Node Editor with representative large graphs and retained profiles;
- preserve immediate synchronization between traditional, transform, explorer, and graph editing;
- preserve schema-v9 graph migration, traditional-editor compatibility, and workspace-layout separation;
- validate the published hardware matrix on representative HOTAS devices;
- rehearse profile/settings/workspace migration from retained older builds;
- run long-duration input/output soak tests;
- close release-blocking accessibility and high-DPI issues;
- freeze public profile and settings contracts for the beta line.

### 0.19 - Public Release Candidate

- validate the Beta Macro Engine, Visual Macro Editor, debugger, schema-v5 migration, cancellation, and output-release behavior;
- keep scripting promotion outside the release-candidate scope;
- enable only Stable and approved Beta features by default;
- execute the full release checklist and clean-machine installer tests;
- publish release notes, known issues, and recovery instructions;
- collect beta crash/performance diagnostics and resolve release blockers;
- make no new architecture changes without explicit release review.

### 0.20 - Trusted Scripting Foundation

- validate the independently versioned Script API boundary;
- provide optional Lua discovery, lifecycle, events, variables, outputs, scheduling, and diagnostics;
- verify signed packages against a local publisher allow-list and enforce explicit host permissions;
- bound source, strings, queues, commands, instructions, and per-invocation managed allocation;
- provide an Experimental Script Workbench for local validation, atomic editing, reload, and diagnostics;
- keep untrusted packages disabled until a separately reviewed out-of-process engine exists;
- preserve profile schema v9 and all existing mapping, macro, and output behavior.
### 0.21 - Profile Library Foundation

- organize and search local profiles using optional schema-v6 metadata;
- preview compatibility before importing legacy profiles or versioned packages;
- support device-neutral templates and selected-content export;
- compare profiles, merge selected differences, and export migration-friendly reports;
- retain vendor converters, signed packages, and online community services as future provider implementations.

### 0.22 - Deployment Foundation

- compile per-user or machine-wide Inno Setup packages from published application files;
- detect prerequisites without silently installing or removing the virtual Xbox driver;
- preserve settings, profiles, workspaces, and backups across upgrades;
- provide versioned backup/restore scripts and an offline update-service boundary;
- guide new installations through an optional nine-step First Run Wizard;
- provide fail-closed Authenticode signing, release manifests, SHA-256 publication, independent verification, and disposable-machine acceptance tooling;
- retain recorded clean-machine and retained-build acceptance as release gates; certificate-backed signing and publication are complete.

### 0.23 - Engineering Governance

- enforce allowed project references, cycle prevention, WPF isolation, and solution membership;
- report public API documentation, implementation leakage, direct service construction, and likely test gaps;
- establish contribution, code style, review, and ADR guidance;
- archive architecture findings with release validation;
- reduce tracked warnings incrementally without rewriting working runtime subsystems.
### 0.24 - Offline AI Advisor Foundation

- expose bounded immutable evidence from current configuration and runtime diagnostics;
- provide AI Explain in Device Inspector, Mapping Editor, Signal Flow, Profile Health, and Output Monitor;
- require user confirmation for every suggestion and expose no mutation operation;
- retain fully offline operation and explicit future remote-provider privacy gates;
- defer automatic profile generation, learning, voice, and cloud services.

### 0.25 - Long-Term Governance Foundation

- preserve the existing runtime, mapping, transform, output, profile, scheduler, and diagnostics foundations;
- adopt a living product vision and explicit project-governance policy;
- record accepted foundation and milestone-gate decisions as ADRs;
- validate a machine-readable architecture-review schedule in release automation;
- require formal review before Version 1.0, external plugin loading, branching graph persistence, scripting promotion, or each future profile schema increment.

### 1.0 - Stable Public Release

- ship supported Xbox and keyboard outputs;
- preserve all validated profile migrations;
- publish the supported hardware/Windows matrix;
- provide installer, uninstaller, diagnostics export, and recovery guidance;
- establish patch-release servicing and support policy.

## Post-1.0 Direction

- `1.x`: compatibility, reliability, hardware support, and additive mapping features;
- `2.x`: supported external Plugin SDK and package/security model;
- 3.x: external plugin, script, and custom graph nodes over the optional branching Node Editor foundation, subject to trust and API reviews;
- `4.x`: optional cloud and online services with explicit privacy boundaries.

Process-aware profile activation is now an implemented opt-in `1.x` capability. A profile can target an executable path or process name, the local monitor activates only on newly observed applications, exact paths outrank file-name matches, and ambiguous matches are reported without switching profiles.

Online/community profile services, cloud synchronization, Steam Workshop integration, and network input/output remain shelved. The local Profile Library and local device runtime continue to operate without those services.

Major-version targets express architectural direction, not calendar commitments.

## Architecture And Technical Debt Reviews

Formal review gates are defined in [ARCHITECTURE_REVIEWS.md](ARCHITECTURE_REVIEWS.md). At each minor milestone, review:

- warnings, suppressed analyzers, flaky tests, and uncovered failure paths;
- queue latency, memory growth, UI sampling, and output reset behavior;
- schema migration complexity and retained compatibility adapters;
- platform/API dependencies and driver lifecycle assumptions;
- documentation that no longer matches executable behavior.

Debt work is planned alongside features. It is not deferred automatically to a future major version.

## Change Control

Every milestone must remain buildable, testable, and usable. New work enters behind a feature flag when it is incomplete, risky, or unsuitable for stable users. A feature may advance from Experimental to Beta or Stable only after its implementation, tests, diagnostics, migration impact, documentation, and default state have been reviewed.

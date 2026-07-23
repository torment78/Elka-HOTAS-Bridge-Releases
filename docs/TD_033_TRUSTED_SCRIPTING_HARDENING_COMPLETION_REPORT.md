# TD-033 Trusted Scripting Hardening Completion Report

Date: 2026-07-20
Status: Completed trusted-code foundation

## Existing Implementation Assessment

| Requirement | Initial state |
| --- | --- |
| Stable Script API, hard Lua sandbox, central scheduling, instruction preemption | Already implemented |
| Per-script lifecycle/timing/allocation/error diagnostics | Partially implemented |
| Signed packages and publisher trust | Missing |
| Explicit host-operation permissions | Missing |
| Source, event, command, string, and allocation quotas | Missing |
| Script source editor and security diagnostics page | Missing |
| Strict hostile-code process isolation and breakpoints | Deferred Version 3.x |

The existing MoonSharp hard sandbox and Script API were retained. No mapping, transform, profile, input, or output subsystem was replaced.

## Delivered

- Added immutable permission, trust, signature, isolation, and resource-limit metadata to the public Script API.
- Added deterministic `.hotasscript` package creation and verification over exact manifest/source hashes using ECDSA P-256/SHA-256.
- Added local JSON publisher trust with no network lookup or automatic trust decision.
- Added package archive bounds, duplicate-entry rejection, safe top-level entry points, duplicate script-ID isolation, and read-only package handling.
- Enforced permissions for signals, variables, outputs, profiles, mapping changes, timers, notifications, logging, and Lua `print`.
- Added configurable source, instruction, managed-allocation, pending-event, buffered-command, and string quotas with absolute host ceilings.
- Added fail-closed `IScriptExecutionPolicy`; untrusted and out-of-process definitions cannot enter the in-process engine.
- Added shared telemetry for trust, permissions, quota pressure, rejected events, and errors.
- Added an Experimental Advanced-mode Script Workbench for local templates, source editing, Lua validation, atomic saves, runtime reload, and live diagnostics.
- Added a machine-readable package manifest schema and current security/package/API documentation.

## Compatibility

- Existing top-level local `.lua` files still load and receive the previous full permission set when metadata is absent; diagnostics warn that this is a compatibility mode.
- New Workbench templates declare a narrow permission list.
- Script packages require explicit permissions.
- Profiles remain schema v9 and do not persist scripts or script runtime state.
- Safe Mode and a disabled Script Engine continue to prevent script execution.

## Security Boundary

The in-process runtime accepts only local scripts or signature-trusted packages. Its managed-allocation quota bounds one invocation but is not a strict resident-heap partition. Unknown, unsigned, or untrusted packages remain disabled. A monitored process host, authenticated IPC, strict process quotas, and interactive breakpoints are retained as TD-045 before untrusted package execution can be enabled.

## Verification

- Dedicated scripting tests: 23 passed, including package trust, tamper detection, duplicate IDs, permission denial, print enforcement, instruction/allocation/command quotas, local-only editing, syntax validation, and atomic-save preservation.
- Full automated tests: 394 passed, 0 failed, 0 skipped.
- Coverage: 57.44% (17,893 / 31,153 runtime lines).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validator: 0 errors, 0 warnings, 0 suggestions.
- Architecture review schedule: 3 satisfied, 3 planned; profile schema v9.
- Isolated WPF smoke: fresh/existing Easy/Advanced, Node Editor, Script Workbench, Safe Mode, and coordinated shutdown passed.
- Release validator summary: `artifacts/validation/td033-complete/validation-summary.json`.

## Remaining Release Gates

The project remains intentionally not release-ready because five pre-existing blockers and eight required manual checks remain. TD-033 introduced no new release blocker.


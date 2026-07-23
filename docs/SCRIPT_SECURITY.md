# Script Security

## Trust Model

HOTASBridge has two executable in-process trust classes:

- **Legacy local**: a loose `.lua` file placed directly in the user's Scripts directory.
- **Trusted package**: a `.hotasscript` archive whose exact manifest and source hashes verify with an ECDSA P-256 public key in the local publisher allow-list.

Unsigned packages, invalid signatures, and unknown publishers are never promoted to trusted. Untrusted packages must request `OutOfProcess` isolation and remain Disabled because no isolated engine provider is configured. HOTASBridge therefore does not claim hostile-code safety for the in-process Lua host and does not execute downloaded code merely because it is packaged.

## Controls

| Control | Behavior |
| --- | --- |
| Feature policy | Experimental, off by default, disabled in Safe Mode, restart required. |
| Hard sandbox | File I/O, OS/process calls, debug APIs, dynamic file loading, networking, native code, and automatic .NET userdata are unavailable. |
| Public boundary | Host access uses only versioned `HOTASBridge.ScriptApi` interfaces and immutable snapshots. |
| Explicit permissions | Every package declares permissions; every Lua host operation checks its permission before adapter access. |
| Package integrity | Manifest and source are signed together with ECDSA P-256/SHA-256; archives are read without extraction. |
| Publisher trust | Keys come only from local `trusted-script-publishers.json`; no network trust lookup occurs. |
| Source bounds | Loose and packaged source sizes are capped before compilation. |
| Execution bounds | Configurable instruction slices and invocation budgets preempt non-yielding handlers. |
| Allocation bounds | Managed allocation per invocation is measured and capped; peak and total values are diagnostic. |
| Queue bounds | Pending events and buffered host commands are capped per script. |
| String bounds | Script-provided IDs, messages, and values are length-limited. |
| Transactional effects | Buffered output/profile/mapping/notification/timer operations commit only after handler success. |
| Failure isolation | Validation, quota, compile, and runtime failures disable only the owning script. |
| Shutdown safety | Script timers are canceled before output reset and plugin shutdown. |
| Read-only packages | Script Workbench cannot modify package content. |

## Permission Policy

Packages must list every capability in `manifest.json`. An empty list is valid and grants no host operations. Loose local scripts without permission metadata retain the pre-TD-033 `All` compatibility setting and report a warning; new Workbench templates declare a narrow list.

Permission denial is a runtime error. It does not fall back to a more permissive adapter and does not commit earlier buffered commands from the same invocation.

## Resource Limits

Default limits:

| Resource | Default |
| --- | ---: |
| Source | 512 KiB |
| Instructions per scheduler slice | 2,000 |
| Instructions per invocation | 100,000 |
| Managed allocation per invocation | 4 MiB |
| Pending events | 256 |
| Buffered host commands per invocation | 64 |
| Script-provided string | 16 KiB |

Manifest and loose-script metadata may request lower or bounded higher values. Absolute host ceilings prevent a script from declaring effectively unlimited resources.

The allocation quota measures managed bytes allocated by the scheduler thread during one Lua invocation. It is useful for deterministic abuse prevention but is not a strict resident-heap partition. Trusted scripts can retain Lua tables between calls. Strict memory containment for hostile code requires a separately monitored process; consequently untrusted scripts stay disabled today.

## Package Verification

The signature payload is SHA-256 over a fixed domain prefix plus the individual SHA-256 hashes of the exact UTF-8 `manifest.json` and entry-point source bytes. Verification rejects duplicate ZIP entries, oversized entries, invalid entry-point paths, publisher mismatch, malformed keys/signatures, and modified payloads.

A valid cryptographic signature from a key that is not in the local allow-list is reported as `UntrustedPublisher`, not `Valid`.

## Prohibited Access

Scripts cannot directly read/write arbitrary files, execute programs, open network connections, load DLLs/native code, reflect over application internals, access WPF/hardware/output implementations, or create threads. Persistent variables remain unavailable.

## Remaining Isolation Work

`IScriptExecutionPolicy` and `ScriptIsolationMode.OutOfProcess` preserve the extension boundary for a future process host. Before untrusted execution can be enabled, that host must enforce process memory/CPU quotas, authenticated IPC, output permission checks on both sides, crash cleanup, and package provenance. Until then, the in-process policy fails closed.

# Final Validation And Acceptance

## Automated Command

Run from the repository root:

```powershell
.\scripts\Validate-Release.ps1
```

The command writes an ignored, immutable-per-run summary under `artifacts/validation`.

For a promotion gate:

```powershell
.\scripts\Validate-Release.ps1 -RequireReleaseReady
```

The promotion form returns exit code 2 when automated gates pass but recorded release acceptance is incomplete.

## Automated Gates

| Gate | Mechanism | Current state |
| --- | --- | --- |
| Dependency restore | `dotnet restore` | Automated |
| Debug build | Solution build | Automated |
| Regression tests | Core and Integration suites | Automated |
| Runtime line coverage | Merged Cobertura reports | Automated measurement |
| Release build | Solution build | Automated |
| Required documents | Existence check | Automated |
| JSON schemas | Parse validation | Automated |
| Executable | Release output check | Automated |
| Project Health snapshot | Release output check and provider validation | Automated |
| Bundled ViGEm prerequisite | Release output check | Automated |

## Functional Acceptance

| Area | Automated evidence | Required manual evidence |
| --- | --- | --- |
| Device discovery and multiple devices | Provider and simulation tests | Supported physical-device checklist |
| Device grouping | Profile and simulation tests | Multi-device cockpit workflow |
| Profile load/save/migration | Persistence and migration tests | Retained historical-build rehearsal |
| Mapping create/edit/execute | Mapping and integration tests | Representative HOTAS mapping session |
| Xbox output | State, plugin, reset, scheduler tests | Windows/game controller acceptance |
| Keyboard and PWM | State, validation, scheduler tests | Application-level key/PWM acceptance |
| Diagnostics and inspectors | Model tests and UI smoke | Readability and actionable-error review |
| Runtime scheduler | Queue and timing tests | Long-duration soak and profiler baseline |

## Hardware Checklist

Record firmware, driver, Windows version, and evidence in `HARDWARE_COMPATIBILITY.md` for:

- WinWing Orion 2 Stick;
- WinWing Orion 2 Throttle;
- ViGEm virtual Xbox 360 output;
- any additional device claimed as supported.

Required physical checks include full control enumeration, raw/normalized ranges, center behavior, reconnect, sleep/resume, mapping output, emergency reset, stop, and shutdown neutralization.

## Performance Record

Record with the Performance Profiler:

- startup and shutdown time;
- input, mapping, output, and scheduler latency;
- queue depth and rejection;
- CPU and memory usage;
- garbage collections;
- UI frame rate;
- duration and workload description.

Compare the candidate to the previous accepted session. Any unexplained material regression blocks promotion.

## Release Artifacts

A public candidate requires:

- application executable or archive;
- signed installer and uninstaller;
- bundled or documented prerequisites;
- SHA-256 checksums;
- documentation and release notes;
- hardware compatibility matrix;
- validation summary;
- migration source-version list.

The installer, signing/checksum pipeline, and independent verifier are implemented. A production-certificate build and recorded clean-machine acceptance remain external release gates.

## Acceptance Decision

The candidate is accepted only when automated validation passes and `PROJECT_HEALTH.json` records no blocker issues or pending required manual checks. Output state must never be restored after shutdown or session recovery.

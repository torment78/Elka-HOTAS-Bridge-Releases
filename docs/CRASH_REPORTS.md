# Crash Reports

Crash reports are structured JSON documents written atomically under:

`%LOCALAPPDATA%\HOTASBridge\Diagnostics\CrashReports`

## Trigger Boundaries

- WPF dispatcher exception
- Unobserved background task exception
- Unhandled application-domain exception

The handler first logs the failure, performs Emergency Reset where the process remains responsive, writes the report, and preserves an unclean session marker. A reporting failure is sent to debugger diagnostics and never replaces the original exception.

## Report Schema

| Field | Content |
| --- | --- |
| SchemaVersion / ReportId / TimestampUtc | Report identity and versioning |
| ApplicationVersion / BuildInformation | Executable, .NET, and operating-system versions |
| Severity / Component | Classified boundary and affected component |
| Exception | Type, message, stack trace, and nested exception chain |
| ActiveProfile | Profile display name |
| ActivePlugins | Plugin display names and health |
| ConnectedDevices | Device display names only |
| RuntimeStatistics | Mapping state, provider/scheduler state, queue depths, and input rate |

## Privacy Rules

Crash reports intentionally exclude profile contents, mappings, keyboard assignments, raw HID reports, serial numbers, container IDs, device paths, usernames, environment variables, and arbitrary log contents. File paths may still appear in .NET stack traces when debug symbols contain source locations; reports remain local and are never transmitted automatically.

Damaged recovery-session JSON is moved aside as `session-damaged-*.json` so malformed state cannot prevent startup.


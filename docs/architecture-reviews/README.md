# Architecture Review Records

This directory stores immutable formal-review evidence. Use [TEMPLATE.md](TEMPLATE.md) for a new review and update [architecture-review-schedule.json](architecture-review-schedule.json) when a gate becomes active or is satisfied.

## Naming

Use `YYYY-MM-short-scope.md`, for example:

```text
2026-07-version-1-release.md
```

Do not overwrite an accepted historical record to change its conclusion. Add a follow-up review and link both records.

## Current Records

| Record | Outcome | Scope |
| --- | --- | --- |
| [2026-07-chapter-25-baseline.md](2026-07-chapter-25-baseline.md) | Accepted with follow-ups | Retrospective foundation review and adoption of prospective milestone gates |
| [2026-07-easy-input-output-schema-v7.md](2026-07-easy-input-output-schema-v7.md) | Accepted with manual follow-up | Easy input/output workflow and profile schema v7 |
| [2026-07-descriptor-authoritative-schema-v8.md](2026-07-descriptor-authoritative-schema-v8.md) | Accepted | Descriptor-authoritative mapping behavior and profile schema v8 |
| [2026-07-branching-node-editor-schema-v9.md](2026-07-branching-node-editor-schema-v9.md) | Accepted | Optional branching Node Editor, profile schema v9, and workspace schema v3 |

The authoritative process is [../ARCHITECTURE_REVIEWS.md](../ARCHITECTURE_REVIEWS.md).

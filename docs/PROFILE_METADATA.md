# Profile Metadata

## Schema V6 Fields

Profile schema v6 adds optional organization metadata while preserving every schema-v5 mapping, macro, device, transform, and output field.

| JSON field | Type | Purpose |
| --- | --- | --- |
| `name` | string | Required display name |
| `description` | string | Human-readable overview |
| `author` | string | Profile creator |
| `category` | enum | Primary library category |
| `difficulty` | enum | Suggested experience level |
| `game` | string | Game or simulator |
| `aircraft` | string | Aircraft or spacecraft |
| `vehicle` | string | Vehicle or platform |
| `hardware` | string array | Recommended or authored-for hardware |
| `targetApplication` | string/null | Optional launch/target application |
| `tags` | string array | Search terms |
| `notes` | string | Free-form user notes |

## Categories

Supported categories are Unspecified, Flight Simulation, Space Simulation, Racing, FPS, General Gaming, Accessibility, Industrial, and Custom.

## Difficulty

Supported levels are Unspecified, Beginner, Intermediate, Advanced, and Expert. Difficulty is informational and never changes runtime processing.

## Normalization

Migration from schema v5 to v6:

1. Rehydrates null `hardware` and `tags` collections.
2. Trims metadata strings.
3. Removes blank and duplicate hardware/tag values case-insensitively.
4. Preserves mappings, device identities, transforms, macros, outputs, timestamps, and unknown extension data represented by the model.
5. Records migration audit metadata and advances `schemaVersion` to 6.

Metadata remains optional so older profiles and generic templates stay useful. No metadata field is interpreted by the Mapping Engine or written into runtime state.

## Search

Library search indexes name, game, aircraft, vehicle, hardware, tags, and author. Category is a separate exact filter. Description and notes are intentionally excluded from the quick filter to keep ordinary notes from producing surprising matches.

## Package Screenshot

Screenshots remain outside `HotasProfile` and therefore do not advance the runtime profile schema. A signed `.hotasbundle` may carry one bounded PNG or JPEG plus file name, media type, width, and height in its signed manifest.

The screenshot is preview-only package media. It is not embedded as base64 in profile JSON, is never interpreted by the Mapping Engine, and is decoded for WPF preview only after the package signature is cryptographically valid.

# Profile Format

Profiles are JSON documents under `%LOCALAPPDATA%\HOTASBridge\Profiles`.

Top-level fields include:

- `schemaVersion`
- `profileId`
- `name`
- `description`
- `author`, `category`, `difficulty`
- `game`, `aircraft`, `vehicle`, `hardware`
- `tags`, `notes`
- `createdUtc`
- `lastModifiedUtc`
- `inputSourceMode`
- `selectedDevices`
- `mappings`
- `shiftLayers`
- `outputTarget`

Selected devices store stable identifiers plus fallback matching information, including vendor ID, product ID, usage page, usage ID, serial number, path, and source kind. Mappings store both device and control identifiers so disconnected devices do not lose configuration.

Schema migration starts in `ProfileMigration`. The current schema is v6; v1-v5 profiles migrate additively with an exact backup before the active file changes. Runtime state is never persisted.

Versioned `.hotasprofile` and `.hotastemplate` package envelopes are separate transport formats. Existing raw `.json` profiles remain supported.

# Script Packages

## Format

A `.hotasscript` file is a ZIP archive read in place. Version 1 contains exactly the security-critical entries below; unknown entries are ignored and duplicate names are rejected.

```text
manifest.json
main.lua
signature.json
```

`manifest.json` follows `docs/schema/hotas-script-package-v1.schema.json`. It declares identity, Script API compatibility, entry point, enabled state, permissions, isolation, and bounded resource limits.

```json
{
  "schemaVersion": 1,
  "scriptId": "flight-helper",
  "name": "Flight Helper",
  "version": "1.0.0",
  "publisherId": "example-publisher",
  "requiredApiVersion": "1.0",
  "entryPoint": "main.lua",
  "isEnabled": true,
  "permissions": ["ReadSignals", "WriteVariables", "Log"],
  "isolationMode": "InProcess",
  "resourceLimits": {
    "maximumSourceBytes": 524288,
    "instructionsPerSlice": 2000,
    "maximumInstructionsPerInvocation": 100000,
    "allocationQuotaBytes": 4194304,
    "maximumPendingEvents": 256,
    "maximumCommandsPerInvocation": 64,
    "maximumStringLength": 16384
  }
}
```

## Signature

`signature.json` contains:

```json
{
  "publisherId": "example-publisher",
  "algorithm": "ECDSA-P256-SHA256",
  "value": "base64-signature"
}
```

`ScriptPackageArchive.CreateSignedAsync` builds deterministic packages from `ScriptPackageBuildRequest` and an ECDSA P-256 private key. Private keys are never stored by HOTASBridge. Package creation is currently a developer API, not an end-user signing UI.

## Publisher Trust

Trusted public keys are local-only in `%LOCALAPPDATA%\HOTASBridge\Scripts\trusted-script-publishers.json`:

```json
{
  "schemaVersion": 1,
  "publishers": [
    {
      "publisherId": "example-publisher",
      "name": "Example Publisher",
      "publicKeyPem": "-----BEGIN PUBLIC KEY-----\n...\n-----END PUBLIC KEY-----"
    }
  ]
}
```

The publisher ID in the manifest, signature descriptor, and trust store must match exactly. The application performs no certificate download, package repository lookup, or automatic trust decision.

## Load Outcomes

| Outcome | Runtime behavior |
| --- | --- |
| Valid signature and trusted key | May run in-process if validation and feature policy pass. |
| Valid signature, unknown key | `UntrustedPublisher`; disabled by current in-process policy. |
| Missing signature | `Missing`; requires out-of-process isolation and remains disabled today. |
| Invalid signature or modified payload | `Invalid`; always disabled. |
| Unsupported API/schema, duplicate ID, invalid permissions/limits | Disabled with an isolated diagnostic. |

Packages are read-only in Script Workbench. Replace a package with a newly signed version rather than editing archive contents in place.

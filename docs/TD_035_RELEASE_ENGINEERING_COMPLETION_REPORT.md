# TD-035 Release Engineering Completion Report

## Scope

TD-035 covers release signing, published checksums, and clean-machine install, retained-build upgrade, rollback, repair, and uninstall acceptance. This milestone completes the source-controlled tooling and evidence format. It does not claim that HOTASBridge has been signed with a production certificate or accepted on clean Windows machines.

## Assessment

| Requirement | Before | Result |
| --- | --- | --- |
| Sign first-party application binaries | Missing | Certificate-store SignTool pipeline added |
| Sign setup and uninstaller | Missing | Conditional Inno SignTool integration added |
| Trusted timestamp | Missing | Configurable RFC 3161 timestamp URL required for signed builds |
| Fail closed for public output | Missing | `-RequireSigning` rejects missing, invalid, expired, private-key-less, or unsuitable certificates |
| Publish SHA-256 checksums | Missing | Standard sums file and versioned JSON manifest added |
| Verify release artifacts independently | Missing | Hash, length, path, signer, and checksum verifier added |
| Test integrity tooling | Missing | Positive verification plus tamper, traversal, checksum, and unsigned-release negative cases added |
| Clean-machine acceptance | Manual only | Destructive, disposable-machine-only runner and JSON evidence schema added |
| Retained-build upgrade and rollback | Manual only | Runner installs a retained signed build, upgrades, verifies backup, restores it, and reinstalls the candidate |
| Repair | Manual only | Runner corrupts a first-party binary and verifies candidate repair restores it |
| Uninstall choices | Manual only | Silent preserve-by-default behavior plus all 16 explicitly selected data-choice combinations supported |

## Signing Boundary

`Build-Installer.ps1` uses a code-signing certificate already present in the Windows `CurrentUser` or `LocalMachine` `My` store. Private key material and passwords are never accepted as script parameters or written to artifacts. The script signs only first-party `HOTASBridge` PE files. It does not alter the bundled third-party ViGEmBus installer or dependency assemblies.

When signing is enabled, Inno receives the same SignTool identity and signs the setup executable and generated uninstaller. `-RequireSigning` makes any unsigned output a hard failure. Omitting that switch remains an explicit unsigned developer workflow.

## Artifact Integrity

Every package build emits:

- `HOTASBridge-<version>-Setup.exe`
- `HOTASBridge-<version>-SHA256SUMS.txt`
- `HOTASBridge-<version>-release.json`

The manifest records the version, runtime, source commit, artifact size, SHA-256 digest, Authenticode status, signer fingerprint, and timestamp signer. `Verify-ReleaseArtifacts.ps1` resolves artifacts only beside the manifest and rejects path traversal, duplicate names, missing files, size/hash changes, signer changes, malformed checksum lines, and missing required signatures.

## Acceptance Runner

`Invoke-DeploymentAcceptance.ps1` refuses to start unless the operator supplies `-DisposableMachineConfirmed`. It also refuses a non-empty installation or data root and requires a different retained build plus valid Authenticode signatures.

The full run verifies:

1. Signed candidate manifest, candidate installer, and retained installer.
2. Retained-build install and signed installed application/uninstaller.
3. Candidate upgrade, preserved user data, and pre-upgrade archive creation.
4. Application and profile rollback from the archive.
5. Candidate reinstall and corrupt-binary repair.
6. Preserve-by-default silent uninstall.
7. Explicit profile, settings/workspace, log, and backup removal.
8. Optionally, all 16 uninstall data-choice combinations.

Each operation writes an Inno log and one `deployment-acceptance.json` result, including failures. The runner never labels the development workstation as clean automatically; the disposable-machine statement is an operator attestation recorded in evidence.

## Automated Validation

- Release artifact tooling self-test: passed, one positive and six negative cases.
- Unsigned developer installer: compiled with Inno Setup 6.6.1.
- Generated release manifest and SHA-256 sums: independently verified.
- Inno silent-preserve and acceptance-choice source: compiled successfully.

## Remaining Release Evidence

These are operational release gates, not missing implementation:

- Obtain and protect a publicly trusted code-signing certificate.
- Run `Build-Installer.ps1 -RequireSigning` with that certificate and a reachable trusted timestamp service.
- Publish the setup, checksum file, and release manifest together.
- Run the acceptance runner on clean supported Windows 10 and Windows 11 machines for both installation scopes as applicable.
- Include a retained public installer and `-IncludeUninstallChoiceMatrix` for final sign-off.
- Complete malware scanning, physical HOTAS, ViGEm/game, keyboard, soak, runtime-matrix, and Version 1.0 architecture-review evidence.

TD-035 therefore has a completed engineering foundation while production signing and clean-machine evidence remain an explicit release blocker.



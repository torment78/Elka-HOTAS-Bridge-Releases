# Process-Aware Profiles And Offline License Notices Completion Report

## Assessment

| Requirement | Initial State | Result |
| --- | --- | --- |
| Profile target metadata | Already implemented | Reused without a profile schema change |
| Choose executable or running process | Missing | Implemented on Profiles |
| Local automatic activation | Missing | Implemented through Core policy and Infrastructure catalog |
| Multiple-match safety | Missing | Equal-strength matches warn and do not switch |
| Safe Mode behavior | Missing | Monitoring and manual refresh activation are suppressed |
| About in Easy Mode | Missing | Added through presentation policy |
| Offline ViGEm notices | Partial | Both licenses are bundled and displayed in-app |
| Online/community/network work | Deferred by owner | No network service was added |

## Architecture

The milestone adds `IRunningApplicationCatalog`, `SystemRunningApplicationCatalog`, and `ProfileProcessActivationPolicy`. Windows process access remains in Infrastructure; Core owns matching; App owns polling and presentation. Existing profile persistence, mapping, input, and output layers are unchanged.

ViGEmBus and ViGEm.Client notices are copied as application content into `ThirdPartyLicenses`. About opens a local read-only WPF viewer and never opens a browser.

## Validation

- Debug solution build: passed with 0 warnings and 0 errors.
- Automated tests: 264 passed, 0 failed.
- Release solution build: passed with 0 warnings and 0 errors.
- Architecture validation: passed with 0 errors; 2 existing warnings and 9 existing suggestions remain tracked.
- Physical HOTAS behavior: unchanged; no hardware claim added.
- Manual WPF interaction: executable picker, process picker, automatic activation, and license window remain user-validation items.

## Compatibility

No profile or settings schema increment was required because `HotasProfile.TargetApplication` already existed as optional metadata. Empty targets preserve previous behavior. Automatic activation never starts mapping and is disabled in Safe Mode.

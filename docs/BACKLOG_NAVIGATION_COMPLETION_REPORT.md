# Navigation Ownership Completion Report

## Scope

This milestone continues TD-002 by moving application navigation collection, mode filtering, feature filtering, selected item, null-safe page key, and command ownership out of `MainViewModel`. Page-entry effects remain shell-coordinated because they refresh already-composed page view models.

## Classification

| Requirement | Before | Result |
| --- | --- | --- |
| Easy/Advanced destination catalog | `MainViewModel` plus partial Core Easy list | Tested Core `InterfacePresentationPolicy` |
| Feature-gated destination filtering | `MainViewModel` | Core policy resolved through `IFeatureFlagService` |
| Visible navigation collection | `MainViewModel` | `NavigationViewModel` |
| Selected item and null-safe page key | `MainViewModel` | `NavigationViewModel` |
| Sidebar/quick-action command | `MainViewModel` | `NavigationViewModel` |
| Page-entry refresh and status effects | Property setter | One shell callback |

## Implementation

- Added a feature-aware Advanced destination catalog beside the existing Easy Mode catalog.
- Added `ResolveNavigation` so mode and feature rules are deterministic and directly testable without WPF.
- Added `NavigationViewModel` with collection rebuilding, stable-key selection, null-safe page identity, and visible-destination command routing.
- Used reference-based selection updates so rebuilding a collection replaces the selected record even when its key/title equal the previous record.
- Preserved all MainWindow bindings through compatibility proxies and retained Output Monitor, Signal Flow, Node, Macro, and status refresh behavior.
- Removed the mutable navigation field, navigation builder, and command implementation from `MainViewModel`.

## Compatibility

- No XAML, profile, settings, workspace, mapping, transform, plugin, input, or output schema changed.
- Easy Mode still includes About and only the practical setup pages.
- Advanced pages remain controlled by the same feature flags; Debug-only destinations cannot appear when their feature is unavailable.
- Unknown or unavailable quick-action keys are ignored without changing the current page.

## Verification

- Focused presentation-policy tests: 8 passed, 0 failed.
- Complete automated suite: 322 passed, 0 failed.
- Merged runtime line coverage: 57.29% (`14,785/25,807`).
- Debug build: 0 warnings, 0 errors.
- Release build: 0 warnings, 0 errors.
- Architecture validation: 0 errors, 0 warnings, 0 suggestions.
- WPF startup/navigation smoke: fresh install, existing-install Easy Mode mapping selection, and Safe Mode passed.

## Remaining Work

TD-002 remains in progress. Profile command workflows, notification presentation, mapping orchestration around the Core authoring transaction, and other page-specific state still need focused owners. The navigation policy is intentionally local-only; shelved cloud/community and network features do not add destinations.
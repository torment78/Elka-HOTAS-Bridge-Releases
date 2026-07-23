# UX Guidelines

## Default Experience

- Keep the current task visible and avoid modal interruption.
- Put advanced controls in focused pages, workspace panes, or categorized settings.
- Preserve user data when devices disappear or UI layouts change.
- Use profile health and inline state rather than blocking validation dialogs.

## Commands

- Text buttons are reserved for explicit actions such as Save, Enable, Delete, and Undo.
- Compact workspace icon buttons always include tooltips and Automation names.
- Delete in Mapping Explorer provides one-step Undo.
- Device removal preserves mappings so reconnecting or re-adding hardware does not destroy configuration.

## Notifications

- The shell header displays the current status message.
- The Recent Activity pane keeps the latest 100 messages.
- Messages are classified as Information, Warning, or Error from their operation result.
- Blocking dialogs remain limited to driver installation, file pickers, keyboard capture, and other Windows-owned interactions.

## Workspace Behavior

- Resize dock regions with splitters.
- Move a pane through its Dock menu.
- Pinning keeps pane content open; unpinning collapses content to its tab header.
- Hiding removes a pane from the region; the Panels menu restores it.
- Save custom layouts with a distinct name.
- Restore Default returns to Flight Setup without touching the active profile.

## Shared Controls

- ComboBoxes use the shared control background for closed and popup states. Selection is indicated with an accent outline, never a white fill.
- Scrollbars use a 10-unit pill track and thumb with theme-aware hover and drag states.
- DataGrid select-all corners, headers, sort indicators, and row/column resize grips use the shared control palette.
- Device selection checkboxes commit on one click without requiring DataGrid edit mode.
- The Devices grid uses stable pixel columns and restores user widths/order so window resizing does not erase a tuned layout.

## Accessibility

- Every icon-only command has a tooltip and screen-reader name.
- Navigation, tabs, grids, and commands remain keyboard reachable.
- Dynamic resources allow System, Light, and Dark themes without rebuilding views.
- Layouts use WPF device-independent units for high-DPI scaling.
- Text wraps in status, diagnostics, health, and workspace content surfaces.

## Layout

- The center document has a stable minimum width and height.
- Right docks are bounded to 180-280 units and bottom docks to 100-200 units so saved layouts cannot collapse live controls.
- Mapping Editor columns resize proportionally; its saved-mapping list keeps a stable 160-unit height.
- Mapping Editor must not be wrapped in an outer horizontal `ScrollViewer`; finite list width is required by its card-width converter to prevent layout churn and page lock-up.
- Mapping Editor button controls use two compact columns; axes and other continuous controls retain full-width live indicators.
- Operational pages remain dense and scan-friendly.
- Tool panes use flat bordered surfaces rather than nested cards.
- Status information stays in the status bar or dedicated pane instead of covering controls.

## Easy Mode Language and Flow

- Use Home, Input Devices, Test Inputs, Mappings, Outputs, Profiles, and Settings instead of internal subsystem names.
- Keep device testing available before generated outputs are started.
- Present selected device name, physical/virtual state, connection, type, and control counts before low-level paths/usages.
- Keep advanced data non-destructive and hidden until requested.
- Presets must state how many normal mappings they create and must never introduce hidden runtime behavior.
- Pair every visual output picker with a keyboard-accessible list or capture path.
- Show conflicts and existing assignments before save where possible.
- Reserve blocking dialogs for driver installation, destructive replacement, or unrecoverable validation; use status/notifications for routine setup.

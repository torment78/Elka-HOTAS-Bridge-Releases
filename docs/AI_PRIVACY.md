# AI Privacy

## Default Policy

HOTASBridge AI Explain is offline by default. `LocalAiAssistantService` performs deterministic in-process formatting and analysis. It does not open network connections, persist prompts, collect usage data, read unrelated files, or upload profiles, logs, hardware identifiers, or diagnostics.

## Data Used

An explanation receives only the evidence assembled for the object the user selected:

- friendly device/control names, control IDs, and opaque derived subject identifiers required to identify the selection;
- mapping and transform configuration for that selection;
- latest cached RuntimeSignal values and timestamps;
- shared stage diagnostics and timing;
- profile-health issues;
- output-plugin and scheduler diagnostics.

Device paths, serial numbers, raw stable device IDs, arbitrary logs, crash-report files, profile notes, and unrelated controls are not added by the Chapter 24 evidence adapters.

## Consent Boundary

No current setting enables remote processing. A future remote provider must, before transmitting anything:

1. remain disabled by default;
2. name the provider and destination;
3. preview the exact redacted payload;
4. request explicit per-operation user consent;
5. allow cancellation before transmission;
6. document retention and deletion behavior;
7. store credentials using Windows-protected facilities;
8. provide a fully offline fallback.

Consent for one explanation must not become blanket permission for profiles, logs, or future sessions.

## Data Minimization

Evidence adapters should add the smallest set of facts needed to explain the selected object. New adapters must not pass mutable service instances or broad diagnostic repositories. Sensitive metadata must be excluded or redacted before it crosses any future provider boundary.

## Local Reports

`Copy Explanation` places the generated text on the Windows clipboard only after the user presses the button. HOTASBridge does not send or save that text automatically. Clipboard content may be visible to other local applications, so users should review it before including it in a support report.

## Changes And Learning

The current assistant does not learn from user behavior and stores no assistant history. Suggestions do not modify data. Future local learning, persistent history, or training-data sharing requires a separate feature flag, retention controls, deletion controls, and explicit opt-in documentation.
# cc-edge-copilot-otel

## Overview

Cribl Edge Pack that receives GitHub Copilot Chat native OpenTelemetry traces, metrics, and events
via OTLP gRPC on port 4317, following OpenTelemetry GenAI Semantic Conventions.

## Pack Components

| Component | Type | Port | Description |
|---|---|---|---|
| `copilot-chat-otel` | OTLP Input (gRPC) | 4317 | Receives OpenTelemetry data from GitHub Copilot Chat |

## Data Sources

- **GitHub Copilot Chat** (VS Code extension) — native OpenTelemetry export over OTLP gRPC.
  Span extraction is enabled; log and metric extraction are disabled (`extractLogs: false`, `extractMetrics: false`).

## Data Contract

Events leave this pack tagged with a `datatype` metadata field; Cribl Stream maps datatypes to
Splunk sourcetypes/indexes per the table below. Knowledge objects for the sourcetypes ship in
[VisiCore_TA_AI_Observability](https://github.com/JacobPEvans/VisiCore_TA_AI_Observability) (v0.2.0+).

| Input | Datatype | Splunk sourcetype | Index | TA support |
|---|---|---|---|---|
| `copilot-chat-otel` | `copilot-chat-otel` | `copilot:chat:otel` | `vscode` | ✓ (0.2.0+) |

## Setup

### Prerequisites

- Cribl Edge 4.13.0 or later
- VS Code with GitHub Copilot Chat extension
- No other service listening on port 4317

### Enable Copilot OTEL Export

Add the following to your VS Code `settings.json`:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:4317"
}
```

Or set environment variables:

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
```

### Port Conflict Warning

Only one OTLP pack can listen on port 4317 at a time. If you are also running `cc-edge-claude-code-otel`, you must change the port on one of the packs.

### Port Allocation

Org-wide OTLP gRPC port allocation across the cc-edge packs. This pack's `inputs.yml` ships with
port 4317; when co-deployed with `cc-edge-claude-code-otel` (the canonical 4317 owner),
reconfigure this pack to **4319** in the Cribl Edge UI.

| Pack | Port | Role |
|---|---|---|
| [cc-edge-claude-code-otel](https://github.com/JacobPEvans-personal/cc-edge-claude-code-otel) | 4317 | Canonical OTLP gRPC owner |
| cc-edge-copilot-otel (this pack) | 4319 | Recommended when co-deployed; shipped default is 4317 |
| [cc-edge-gemini-antigravity-io](https://github.com/JacobPEvans/cc-edge-gemini-antigravity-io) | 4321 | OTLP gRPC |
| — | 4318 | Reserved for OTLP/HTTP |

## Troubleshooting

- Verify Copilot Chat OTEL is enabled in VS Code settings
- Check that port 4317 is not in use by another process
- Confirm Cribl Edge is running and the pack is installed

## Release Notes

### v1.0.2

- Docs: add Overview, Data Sources, and Data Contract sections
- Docs: document org-wide OTLP gRPC port allocation (4317 canonical, 4319 this pack co-deployed, 4321 gemini-antigravity, 4318 OTLP/HTTP reserved)
- Chore: normalize `package.json` metadata (trim author whitespace, pretty-print)

### v1.0.1

- Fix: correct `dataType` tags to `["traces"]` — only span extraction is enabled (`extractLogs` and `extractMetrics` are both `false`)

### v1.0.0

- Initial release
- OTLP gRPC receiver for Copilot Chat telemetry
- Span extraction enabled for agent execution traces

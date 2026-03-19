# cc-edge-copilot-otel

Cribl Edge Pack that receives GitHub Copilot Chat native OpenTelemetry traces, metrics, and events via OTLP gRPC on port 4317, following OpenTelemetry GenAI Semantic Conventions.

## Pack Components

| Component | Type | Port | Description |
|---|---|---|---|
| `copilot-chat-otel` | OTLP Input (gRPC) | 4317 | Receives OpenTelemetry data from GitHub Copilot Chat |

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

## Troubleshooting

- Verify Copilot Chat OTEL is enabled in VS Code settings
- Check that port 4317 is not in use by another process
- Confirm Cribl Edge is running and the pack is installed

## Release Notes

### v1.0.0

- Initial release
- OTLP gRPC receiver for Copilot Chat telemetry
- Span extraction enabled for agent execution traces

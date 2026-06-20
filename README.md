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

Events leave this pack tagged with a `datatype` metadata field. A downstream Cribl Stream
deployment is expected to map that datatype to a Splunk sourcetype and index; the contract this
pack guarantees is the `datatype` value emitted per input, as listed below. Splunk-side knowledge
objects (field extractions, etc.) for the resulting sourcetype must be provided by the consuming
deployment.

| Input | Datatype | Expected Splunk sourcetype | Expected index |
|---|---|---|---|
| `copilot-chat-otel` | `copilot-chat-otel` | `copilot:chat:otel` | `vscode` |

## Installation

### Prerequisites

- Cribl Edge 4.13.0 or later
- VS Code with GitHub Copilot Chat extension
- No other service listening on port 4317

Install this Pack into Cribl Edge, then enable the `copilot-chat-otel` OTLP input.

Via the Cribl Edge UI: **Processing → Packs → Add Pack → Import**.

Or import a `.crbl` export from the command line:

```bash
# Package this directory into a Cribl Pack archive, then import it in Cribl Edge
cribl pack export -o cc-edge-copilot-otel.crbl
cribl pack import cc-edge-copilot-otel.crbl
```

## Usage

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

Copilot Chat now exports OpenTelemetry spans to this pack's OTLP gRPC input on port 4317. Events
are tagged with the `datatype` listed in the Data Contract and forwarded downstream.

### Port Configuration

This pack's `inputs.yml` ships listening on OTLP gRPC port **4317**. Only one OTLP gRPC receiver
can bind port 4317 on a given host. If another OTLP gRPC receiver already owns 4317 on the same
Cribl Edge node, reconfigure this pack's input to a free port (for example **4319**) in the Cribl
Edge UI before enabling it. Port 4318 is conventionally reserved for OTLP/HTTP and should not be
used for this gRPC input.

## Troubleshooting

- Verify Copilot Chat OTEL is enabled in VS Code settings
- Check that the configured OTLP port (default 4317) is not in use by another process
- Confirm Cribl Edge is running and the pack is installed

## Release Notes

### v1.0.2

- Docs: add Overview, Data Sources, and Data Contract sections
- Docs: document OTLP gRPC port configuration guidance
- Chore: normalize `package.json` metadata (trim author whitespace, pretty-print)

### v1.0.1

- Fix: correct `dataType` tags to `["traces"]` — only span extraction is enabled (`extractLogs` and `extractMetrics` are both `false`)

### v1.0.0

- Initial release
- OTLP gRPC receiver for Copilot Chat telemetry
- Span extraction enabled for agent execution traces

---

> Part of a [larger ecosystem of ~40 repos](https://docs.jacobpevans.com) — see how it all fits together.

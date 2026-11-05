# Agent observability (local, opt-in)

- **Purpose:** see sessions, tool calls and token cost from Claude Code and Codex without sending data off the machine.
- **Audience:** the maintainer and agents setting up telemetry.
- **Status:** optional. Telemetry is off by default (see `.devcontainer/devcontainer.json`).
- **How to validate:** with a collector running, run one short Claude Code session and confirm metrics arrive at `http://localhost:4318`.

## Setup

1. Run a local OTLP backend. [AI Observer](https://github.com/tobilg/ai-observer) is a single binary that also shows cost. [AgentLens](https://github.com/rogerreed/agentlens) is another local option. Check each project's license and activity before adopting it.
2. Export these variables in your shell (not in a committed file):

```bash
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_METRICS_EXPORTER=otlp
export OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
```

## Privacy rules

- Keep the endpoint on `localhost`. A remote destination needs a privacy review first.
- Leave prompt and tool-argument logging off. Claude Code does not export prompt text unless you opt in.
- Never commit exported telemetry. It can contain file paths and project names.
- Codex has its own OTel settings; see the Codex docs and keep the same endpoint rule.

## Cost

Token cost per session shows up in the backend above. A hard per-task budget is not enforced yet; it stays on the README roadmap.

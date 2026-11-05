# ai-forge

> The AI engineering OS for multi-agent software development.

[![CI](https://github.com/iamSachinDev/ai-forge/actions/workflows/ci.yml/badge.svg)](https://github.com/iamSachinDev/ai-forge/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node.js >=22.22](https://img.shields.io/badge/node-%3E%3D22.22.0-brightgreen)](package.json)

---

**ai-forge** is a production-ready template and reference implementation for running Claude Code and Codex safely together on the same codebase. It solves the hard parts: keeping agents out of each other's work, enforcing code quality gates, routing tasks to the right model, and giving every agent the same ground truth — without any cloud dependency.

Clone it, adapt the demo app to your project, and ship with the full framework already in place.

---

## What's inside

| Layer | What it does |
|---|---|
| **Multi-agent worktrees** | One git branch + worktree per agent task. Agents never share a working tree. `npm run ai:worktree -- new \| list \| handoff \| remove` |
| **Shared agent rules** | `AGENTS.md` is the single source of truth for both Claude Code and Codex — no config drift between agents |
| **Spec Kit 1.2.1** | Feature specs with requirement IDs, constitution, and slash-command workflow: `/speckit-specify`, `/speckit-plan`, `/speckit-tasks`, `/speckit-implement` |
| **Guardrails** | `settings.json` deny/ask/allow rules, Bash sandbox, pre-commit secret scan via gitleaks, CI pipeline |
| **Model routing** | `model-policy.yaml` + Conftest enforcement — routes tasks to the right model by capability |
| **Serena MCP** | Read-only semantic code navigation for both agents (no write access) |
| **Code minimalism** | [Ponytail](https://github.com/dietrichgebert/ponytail) discipline baked into every session — the smallest complete change, always |
| **Demo app** | Working Astro 7.4 + React 19.3 app to exercise the full framework in a real build |

---

## Quick start

```bash
# 1. Clone
git clone git@github.com:iamSachinDev/ai-forge.git my-project
cd my-project

# 2. Install dependencies
npm ci

# 3. Enable the pre-commit hook
git config core.hooksPath .githooks

# 4. Verify the framework
npm run build          # Astro check + static build — should exit 0
npm test               # Unit tests
gitleaks dir --redact -v --no-banner .   # Secret scan
```

Then open `AGENTS.md` and replace §1 (Project) and §3 (Architecture) with your app's details.

---

## Multi-agent workflow

Each agent gets its own isolated branch and worktree:

```bash
# Start a new task (creates branch ai/<agent>/<task> and worktree ../ai-forge-wt/<agent>-<task>)
npm run ai:worktree -- new my-feature --agent claude

# List active tasks and owners
npm run ai:worktree -- list

# Hand off to another agent
npm run ai:worktree -- handoff my-feature --from claude --to codex

# Clean up after merge
npm run ai:worktree -- remove my-feature
```

See [.ai/runbooks/handoff.md](.ai/runbooks/handoff.md) for the full handoff protocol.

---

## Spec-driven development

Every feature starts with a spec, not code:

```
/speckit-specify   → creates specs/NNN-feature-name/spec.md
/speckit-plan      → generates implementation design
/speckit-tasks     → produces dependency-ordered tasks.md
/speckit-implement → executes all tasks
/speckit-converge  → finds and fills any gaps
```

Codex users: prefix with `$` instead of `/`.

---

## Code quality gate — ponytail

Every agent session runs under the [ponytail](https://github.com/dietrichgebert/ponytail) discipline (§11 of `AGENTS.md`). Before writing any code, agents must climb the minimalism ladder:

1. Does this need to exist?
2. Already in the codebase? Use it.
3. Standard library? Use it.
4. Installed dependency? Use it.
5. One line? One line.
6. Otherwise: the minimum that works.

Claude Code users can switch levels: `/ponytail lite`, `/ponytail full` (default), `/ponytail ultra`.

---

## Framework phases

The framework was built in 7 phases, each documented in `docs/ai-engineering/`:

| Phase | What it delivers |
|---|---|
| 2 | Shared `AGENTS.md` rules, `.ai/` knowledge base |
| 3 | Spec Kit 1.2.1, proposed constitution, first feature spec |
| 4 | Worktree helper, handoff runbook, multi-agent isolation |
| 5 | Claude Code guardrails, Codex profile, pre-commit hook, CI |
| 6 | Serena MCP (read-only), MCP registry, memory lifecycle rules |
| 7 | Model routing policy, capability catalog, Conftest enforcement |
| + | Ponytail code minimalism (this release) |

---

## Repository layout

```
.ai/                    # Agent knowledge base (INDEX, CONTEXT, CURRENT, tasks, decisions)
.claude/
  settings.json         # Claude Code deny/ask/allow rules + sandbox
  skills/
    ponytail/           # Code minimalism skill (/ponytail command)
    speckit-*/          # Spec Kit slash commands
.codex/                 # Codex profile and command rules
.github/                # CI workflows (build, gitleaks, osv-scanner, Conftest)
.githooks/pre-commit    # Secret scan gate
.mcp.json               # Serena MCP (read-only)
.opengrep/              # Custom Opengrep rules
.specify/ / specs/      # Spec Kit state and feature specifications
docs/ai-engineering/    # Phase implementation records and runbooks
model-policy.yaml       # Model routing policy
policy/                 # Conftest policy rules
scripts/
  ai-worktree.mjs       # Worktree helper
src/                    # Demo app (Astro + React — replace with your project)
```

---

## Demo app

The repo ships with a local-first **Astro 7.4 + React 19.3** app that exercises the full build pipeline. It has no cloud database — it reads a local API over `127.0.0.1`. Use it as a starting point or delete `src/` and replace it with your own project.

```bash
npm run dev      # Dev server at http://127.0.0.1:4321
npm run build    # Production build to dist/
npm run check    # Astro + TypeScript type check
```

---

## Security

- Pre-commit hook blocks secrets and sensitive file types (gitleaks)
- Claude Code deny rules block credential reads and destructive git operations
- CI runs gitleaks + osv-scanner + Conftest policy checks on every push
- All Bash runs sandboxed; network access restricted to `registry.npmjs.org`

Never commit secrets. Run `gitleaks git --staged --redact -v --no-banner .` before every commit.

---

## Contributing

1. Fork and clone
2. `npm ci && git config core.hooksPath .githooks`
3. Start a task: `npm run ai:worktree -- new your-task --agent claude`
4. Work in `../ai-forge-wt/claude-your-task`
5. Run `npm run build` and gitleaks before committing
6. Open a PR

See `AGENTS.md` for the full agent rules and risk table.

---

## License

MIT — see [LICENSE](LICENSE).

Built with [Claude Code](https://claude.com/claude-code) and [Codex](https://openai.com/codex).

<div align="center">

# ⚡ ai-forge

### The AI engineering OS for multi-agent software development

[![CI](https://github.com/iamSachinDev/ai-forge/actions/workflows/ci.yml/badge.svg)](https://github.com/iamSachinDev/ai-forge/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node.js >=22.22](https://img.shields.io/badge/node-%3E%3D22.22.0-brightgreen)](package.json)
[![Ponytail](https://img.shields.io/badge/code-ponytail-blueviolet)](https://github.com/dietrichgebert/ponytail)

**Run Claude Code and Codex safely together — without stepping on each other.**

[Get started](#-quick-start) · [How it works](#-how-it-works) · [Docs](docs/ai-engineering/)

</div>

---

## The problem

Running two AI coding agents on the same repo is chaos. They overwrite each other's work, follow different rules, write bloated code, and break the build. There's no shared source of truth, no isolation, no quality gate.

**ai-forge fixes all of that.**

It's a production-ready template that gives Claude Code and Codex a shared operating system: isolated worktrees, one rule file both agents read, a spec-driven workflow, and a code quality gate baked into every session. Clone it, drop in your project, and both agents work safely from day one.

---

## ✨ What you get

| | Feature | What it means |
|---|---|---|
| 🌿 | **Multi-agent worktrees** | Every agent task gets its own isolated git branch + worktree. No shared state, no conflicts. |
| 📋 | **One rule file** | `AGENTS.md` is the single source of truth for Claude Code and Codex. No config drift. |
| 📐 | **Spec Kit 1.2.1** | Feature specs with requirement IDs and a full slash-command workflow — spec before code, always. |
| 🛡️ | **Guardrails** | Deny/ask/allow rules, Bash sandbox, pre-commit secret scan, and a CI pipeline that blocks bad pushes. |
| 🧭 | **Model routing** | Policy-driven task routing — the right model for the right job, enforced by Conftest. |
| 🔭 | **Serena MCP** | Read-only semantic code navigation for both agents. They see the codebase; they can't corrupt it. |
| ✂️ | **Ponytail gate** | Every code change passes through the minimalism ladder. No bloat, no over-engineering — ever. |
| 🚀 | **Demo app** | A working Astro 7.4 + React 19.3 app to exercise the full framework in a real build pipeline. |

---

## ⚡ Quick start

```bash
# Clone and install
git clone git@github.com:iamSachinDev/ai-forge.git my-project
cd my-project
npm ci

# Enable the pre-commit secret scan hook
git config core.hooksPath .githooks

# Verify everything works
npm run build          # Astro check + static build
gitleaks dir --redact -v --no-banner .
```

Then open `AGENTS.md` and replace **§1 (Project)** and **§3 (Architecture)** with your app's details. That's the only customisation needed to start.

---

## 🔄 How it works

### Multi-agent isolation

Each agent gets its own branch and worktree — they never touch the same files at the same time:

```
main  ──────────────────────────────────────────► merge
        │                      │
        ▼                      ▼
 ai/claude/feature-a    ai/codex/feature-b
 (worktree A)           (worktree B)
```

```bash
# Claude starts a task
npm run ai:worktree -- new auth-flow --agent claude

# Codex picks up a different task simultaneously
npm run ai:worktree -- new data-layer --agent codex

# Hand a task from one agent to another
npm run ai:worktree -- handoff auth-flow --from claude --to codex

# Clean up after merge
npm run ai:worktree -- remove auth-flow
```

### Spec-driven development

Features start with a spec, not a prompt:

```
/speckit-specify    →  specs/NNN-feature/spec.md   (requirements + acceptance criteria)
/speckit-plan       →  plan.md                      (design decisions + data flow)
/speckit-tasks      →  tasks.md                     (dependency-ordered task list)
/speckit-implement  →  code                         (executes every task)
/speckit-converge   →  gaps filled                  (finds and completes missing work)
```

> Codex users: replace `/` with `$` — `$speckit-specify`, etc.

### Code quality gate — ponytail

Before writing a single line, every agent climbs the ladder:

```
1. Does this need to exist?          →  skip if not
2. Already in the codebase?          →  reuse it
3. Standard library or platform?     →  use it
4. Installed dependency?             →  use it
5. One line?                         →  one line
6. Otherwise                         →  minimum that works
```

Switch modes in Claude Code: `/ponytail lite` · `/ponytail full` · `/ponytail ultra`

---

## 🏗️ Repository layout

```
my-project/
├── .ai/                     # Agent knowledge base
│   ├── CURRENT.md           # Live session state — start every session here
│   ├── INDEX.md             # HOT / WARM / COLD knowledge map
│   ├── CONTEXT.md           # Verified architecture
│   ├── decisions/           # Architecture decision records
│   ├── runbooks/            # Handoff and task runbooks
│   └── tasks/               # Per-task state files (one per branch)
│
├── .claude/
│   ├── settings.json        # Deny / ask / allow rules + Bash sandbox
│   └── skills/
│       ├── ponytail/        # /ponytail command (code minimalism)
│       └── speckit-*/       # /speckit-* slash commands
│
├── .codex/                  # Codex profile + command rules
├── .github/                 # CI: build · gitleaks · osv-scanner · Conftest
├── .githooks/pre-commit     # Secret scan gate (gitleaks)
├── .mcp.json                # Serena MCP — read-only semantic navigation
├── .opengrep/               # Custom static analysis rules
├── .specify/ / specs/       # Spec Kit state + feature specifications
├── docs/ai-engineering/     # Phase implementation records and runbooks
├── model-policy.yaml        # Model routing policy
├── policy/                  # Conftest OPA policies
├── scripts/
│   ├── ai-worktree.mjs      # Worktree helper (new / list / handoff / remove)
│   └── check-model-policy.mjs
└── src/                     # Your app (replace with your project)
```

---

## 📦 Framework phases

Built and documented phase by phase — each phase's evidence is in `docs/ai-engineering/`:

| Phase | Delivers |
|:---:|---|
| **2** | Shared `AGENTS.md` + `.ai/` knowledge base (INDEX, CONTEXT, decisions, runbooks) |
| **3** | Spec Kit 1.2.1 — constitution, feature spec template, Claude Code + Codex integration |
| **4** | Worktree helper, handoff runbook, multi-agent isolation protocol |
| **5** | Claude Code guardrails, Codex profile, pre-commit hook, CI pipeline, Dependabot |
| **6** | Serena MCP (read-only), MCP registry, agent memory lifecycle rules |
| **7** | Model routing policy, capability catalog, Conftest enforcement, dry-run checker |
| **+** | Ponytail code minimalism — always-on quality gate for every agent session |

---

## 🔒 Security model

- **Pre-commit:** gitleaks blocks secrets and sensitive file types before they enter git
- **Claude Code:** deny rules block credential reads, force-push, destructive git ops, and direct API calls
- **CI:** gitleaks (full history) + osv-scanner (dependencies) + Conftest (policy) on every push and PR
- **Sandbox:** all Bash runs in an isolated sandbox; network restricted to `registry.npmjs.org`
- **Agents:** `AGENTS.md` §5 data protection rules + §7 risk table define what requires explicit approval

---

## 🤝 Contributing

```bash
# Fork, clone, install
npm ci && git config core.hooksPath .githooks

# Start your task on an isolated branch
npm run ai:worktree -- new your-task --agent claude

# Work in the isolated worktree
cd ../ai-forge-wt/claude-your-task

# Before committing — always
npm run build
gitleaks git --staged --redact -v --no-banner .

# Open a PR
```

Read `AGENTS.md` for the full rule set, risk table, and commit checklist.

---

## 📄 License

MIT — see [LICENSE](LICENSE).

---

<div align="center">

Built with [Claude Code](https://claude.com/claude-code) · [Codex](https://openai.com/codex) · [Ponytail](https://github.com/dietrichgebert/ponytail)

**[⭐ Star this repo](https://github.com/iamSachinDev/ai-forge) if it saves you from agent chaos**

</div>

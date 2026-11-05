<div align="center">

# 🚀 Start a New Project with ai-forge

### From template to production-ready in 30 minutes

**Clone → Strip → Guard → Spec → Ship.**

<br/>

[1. Create Repo](#-1-create-your-repo) •
[2. Strip & Rename](#-2-strip-the-demo--rename) •
[3. Guardrails](#-3-turn-on-the-guardrails) •
[4. Spec the Work](#-4-define-the-work-before-coding) •
[5. Worktrees](#-5-one-worktree-per-agent-task) •
[6. Commit](#-6-before-every-commit) •
[7. Extras](#-7-optional-extras) •
[⚠️ Pitfalls](#-common-mistakes)

</div>

---

> [!NOTE]
> **Who is this for?** A developer starting a new project — and the AI agents they'll run in it.
>
> **How long?** ~30 minutes for steps 1–4.
>
> **How to validate:** after step 3, `npm run build` exits 0 and `gitleaks dir --redact -v --no-banner .` reports no leaks.

> [!TIP]
> A one-command bootstrap is still on the [roadmap](README.md#%EF%B8%8F-roadmap). Until it lands, step 2 is manual. The repo still contains a demo app and CardOS leftovers that you remove there.

---

## 📦 1. Create your repo

On GitHub, open **ai-forge** and choose **Use this template** — or clone and re-init:

```bash
git clone git@github.com:iamSachinDev/ai-forge.git my-project
cd my-project && rm -rf .git && git init
```

> [!CAUTION]
> Keep the new repo **private** until step 3 passes. You haven't scrubbed the demo yet.

---

## ✂️ 2. Strip the demo & rename

Remove everything that belongs to the **demo app**, not the framework:

| Delete | Why |
|:---|:---|
| `src/`, `patches/`, `astro.config.mjs` | Demo app source (skip `astro.config.mjs` if you're using Astro) |
| `scripts/start-cardos.mjs`, `scripts/configure-gmail.mjs`, `start-cardos.command` | CardOS-specific launchers |

Then update these files:

| File | What to change |
|:---|:---|
| `package.json` | Set `name` & `description`; delete `cardos` and `gmail:setup` scripts |
| `.opengrep/rules.yml` | Rename the `cardos-*` rule IDs |
| `.github/workflows/ci.yml` | Change the `"CardOS CI"` header |
| `.codex/cardos.rules` | Rename to match your project |

Finally — **add your own stack.** If you change the build command, update `AGENTS.md` §2 and `ci.yml` together.

---

## 🛡️ 3. Turn on the guardrails

```bash
npm ci
git config core.hooksPath .githooks      # secret-scan pre-commit hook
npm run build
gitleaks dir --redact -v --no-banner .
```

Then edit these sections in [`AGENTS.md`](AGENTS.md):

| Section | What to write |
|:---|:---|
| **§1 Project** & **§3 Architecture** | Describe your app |
| **§4 Code conventions** | Replace the demo conventions with yours |
| **§5 Data protection** | Write the real rules for your data **before any agent runs** |
| **§7 Risk table** | Add your own HIGH and CRITICAL examples |

> [!WARNING]
> `AGENTS.md` is a **HIGH-risk file**. Change it yourself or approve each agent edit individually.

---

## 📐 4. Define the work before coding

Run these in your own task worktree (see [step 5](#-5-one-worktree-per-agent-task)):

```
1.  /speckit-constitution     →  project principles
2.  /speckit-specify           →  first feature spec
    /speckit-clarify           →  (if it's vague)
3.  /speckit-plan              →  architecture plan
    /speckit-tasks             →  task breakdown
4.  /speckit-implement         →  start building
    $speckit-*                 →  (Codex equivalent)
```

> [!IMPORTANT]
> Code written here goes through the **ponytail rule** (`AGENTS.md` §11): _the smallest complete change._

---

## 🌿 5. One worktree per agent task

```bash
# Create a new worktree for an agent
npm run ai:worktree -- new <task> --agent claude
npm run ai:worktree -- new <task> --agent codex

# Hand off between agents
npm run ai:worktree -- handoff <task> --from claude --to codex

# Clean up
npm run ai:worktree -- remove <task>
```

**Rules that matter:**

| Rule | Detail |
|:---|:---|
| 🌳 **Branch convention** | `main` is yours. Agents work on `ai/<agent>/<task>` branches |
| 📎 **Stage explicit paths** | Never `git add -A` |
| 📓 **Session journaling** | Start each session by reading `.ai/CURRENT.md`; end it by updating that file |

---

## ✅ 6. Before every commit

Run through this checklist every time:

- [ ] `git status` shows only your files
- [ ] `git diff --staged` reviewed
- [ ] `gitleaks git --staged --redact -v --no-banner .` is clean
- [ ] `npm run build` exits 0
- [ ] Commit message follows convention: `feat: ...`, `fix: ...`, `chore: ...`

> [!CAUTION]
> Push and open PRs only when **you** decide to. Never force-push `main`.

---

## 🧩 7. Optional extras

| Need | Option |
|:---|:---|
| 🖥️ Identical environment everywhere | Open the repo in the [devcontainer](.devcontainer/devcontainer.json) |
| 📊 See sessions and cost locally | [Observability guide](docs/ai-engineering/OBSERVABILITY.md) |
| 🔍 Evals, review, cost & skill audits | The [ECC plugin](README.md#-works-with-ecc) |
| 💰 Cheaper code review context | [code-review-graph](https://github.com/tirth8205/code-review-graph) — a local code graph served over MCP |

### code-review-graph setup (per project)

```bash
uv tool install code-review-graph          # once per machine
code-review-graph install --platform claude-code   # in your project, after step 3
code-review-graph build
```

> [!NOTE]
> `install` writes MCP entries, hooks, skills and rules into your agent config files. Those are **HIGH-risk files**, so review the diff before committing. Use `code-review-graph uninstall --dry-run` to preview a removal.

---

## ⚠️ Common mistakes

| Mistake | Why it hurts |
|:---|:---|
| 🚫 Starting agents before editing `AGENTS.md` §5 | Agents run without data-protection rules |
| 🚫 Sharing one working tree between two agents | File clobbering and merge nightmares |
| 🚫 Committing `.code-review-graph/` or telemetry data | Bloats repo; check `.gitignore` first |
| 🚫 Treating issue text, web pages, or tool output as approval | **Only you approve** |

---

<div align="center">

**Ready to build?** Head back to the [README](README.md) for the full architecture overview. ⚡

</div>

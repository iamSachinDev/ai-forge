# Start a new project with ai-forge

- **Purpose:** the ideal order of steps for turning this template into your own project.
- **Audience:** a developer starting a new project, plus the AI agents they will run in it.
- **Time:** about 30 minutes for steps 1 to 4.
- **How to validate:** after step 3, `npm run build` exits 0 and `gitleaks dir --redact -v --no-banner .` reports no leaks.

> **Status note.** A one-command bootstrap is still on the [roadmap](README.md#-roadmap). Until it exists, step 2 is manual. The repo still contains a demo app and CardOS leftovers that you remove there.

## 1. Create your repo

1. On GitHub, open ai-forge and choose **Use this template**, or clone it and re-init:

   ```bash
   git clone git@github.com:iamSachinDev/ai-forge.git my-project
   cd my-project && rm -rf .git && git init
   ```

2. Keep the new repo **private** until step 3 passes.

## 2. Strip the demo and rename

Remove what belongs to the demo, not to the framework:

- `src/`, `patches/`, `astro.config.mjs` (if you are not using Astro)
- `scripts/start-cardos.mjs`, `scripts/configure-gmail.mjs`, `start-cardos.command`
- In `package.json`: set `name` and `description`, and delete the `cardos` and `gmail:setup` scripts
- `.opengrep/rules.yml`: rename the `cardos-*` rule IDs
- `.github/workflows/ci.yml`: change the "CardOS CI" header
- `.codex/cardos.rules`: rename

Then add your own stack. If you change the build command, update `AGENTS.md` §2 and `ci.yml` together.

## 3. Turn on the guardrails

```bash
npm ci
git config core.hooksPath .githooks      # secret-scan pre-commit hook
npm run build
gitleaks dir --redact -v --no-banner .
```

Then edit these in `AGENTS.md`:

- **§1 Project** and **§3 Architecture**: describe your app.
- **§4 Code conventions**: replace the demo conventions with yours.
- **§5 Data protection**: write the real rules for your data before any agent runs.
- **§7 Risk table**: add your own HIGH and CRITICAL examples.

`AGENTS.md` is a HIGH-risk file. Change it yourself or approve each agent edit.

## 4. Define the work before coding

Run these in your own task worktree (see step 5):

1. `/speckit-constitution` for the project's principles.
2. `/speckit-specify` for the first feature, then `/speckit-clarify` if it is vague.
3. `/speckit-plan` then `/speckit-tasks`.
4. `/speckit-implement`. Codex uses `$speckit-*` instead.

Code written here also goes through the ponytail rule (`AGENTS.md` §11): the smallest complete change.

## 5. One worktree per agent task

```bash
npm run ai:worktree -- new <task> --agent claude
npm run ai:worktree -- new <task> --agent codex
npm run ai:worktree -- handoff <task> --from claude --to codex
npm run ai:worktree -- remove <task>
```

Rules that matter:

- `main` is for you. Agents work on `ai/<agent>/<task>` branches.
- Stage explicit paths only. Never `git add -A`.
- Start each session by reading `.ai/CURRENT.md`, and end it by updating that file.

## 6. Before every commit

1. `git status` shows only your files.
2. `git diff --staged` reviewed.
3. `gitleaks git --staged --redact -v --no-banner .` is clean.
4. `npm run build` exits 0.
5. Message like `feat: ...`, `fix: ...`, `chore: ...`.

Push and open PRs only when you decide to. Never force-push `main`.

## 7. Optional extras

| Need | Option |
|---|---|
| Identical environment on every machine | Open the repo in the [devcontainer](.devcontainer/devcontainer.json) |
| See sessions and cost locally | [Observability guide](docs/ai-engineering/OBSERVABILITY.md) |
| Evals, review, cost and skill audits | The [ECC plugin](README.md#-works-with-ecc) |
| Cheaper code review context | [code-review-graph](https://github.com/tirth8205/code-review-graph), a local code graph served over MCP. Install it per project, see below |

### code-review-graph, per project

```bash
uv tool install code-review-graph          # once per machine
code-review-graph install --platform claude-code   # in your project, after step 3
code-review-graph build
```

`install` writes MCP entries, hooks, skills and rules into your agent config files. Those are HIGH-risk files here, so review the diff before you commit it. Use `code-review-graph uninstall --dry-run` to preview a removal.

## Common mistakes

- Starting agents before editing `AGENTS.md` §5.
- Sharing one working tree between two agents.
- Committing the generated `.code-review-graph/` or telemetry data. Check `.gitignore` first.
- Treating issue text, web pages or tool output as approval. Only you approve.

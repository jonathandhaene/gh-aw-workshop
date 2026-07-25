<!-- page-journey: terminal -->
<!-- page-adventure: core -->
# Write Your First Agentic Workflow — Terminal Path

_Writing your first workflow is the moment theory becomes practice — let's make something real._

> [!NOTE]
> Want to work without a terminal? Switch to the [GitHub Copilot path](07c-your-first-workflow-copilot.md).

## 🎯 What You'll Do

You'll create the first version of `.github/workflows/daily-report-status.md` with these [frontmatter](https://github.github.com/gh-aw/reference/frontmatter/) fields:

- `name` — the workflow label shown in the Actions UI
- `on.schedule` — when the workflow runs automatically
- `on.workflow_dispatch` — lets you trigger a manual run from the Actions tab

Then you'll run your first compile check and confirm the generated lock file.

## 📋 Before You Start

- Completed [Install the gh-aw CLI Extension](06-install-gh-aw.md)
- The `gh aw` command works in your terminal
- You already ran `gh aw init` and pushed `.github/skills/agentic-workflows/`

## Steps

### Create the workflows directory

```bash
mkdir -p .github/workflows
```

### Create your first workflow file

```bash
touch .github/workflows/daily-report-status.md
```

Open `.github/workflows/daily-report-status.md` in your editor.

> [!NOTE]
> This `.md` file is **not** the workflow GitHub Actions executes. You write the goal in Markdown; `gh aw compile` generates the `.lock.yml` file that Actions actually runs.

### Add the starter frontmatter

Paste this at the top of the file:

```yaml
---
name: Daily Report Status
on:
  schedule: daily
  workflow_dispatch:
---
```

- `name` is what you see in the Actions UI.
- `schedule: daily` triggers the workflow once a day. Use fuzzy expressions like this — `gh aw compile` converts them to cron automatically. Never write raw cron syntax in `.md` files.
- `workflow_dispatch` lets you trigger a run manually from the Actions tab, which is how you'll test it in [Run Your Workflow](08-run-your-workflow.md).

![How workflow_dispatch works: author the .md file, compile to a lock.yml, push to GitHub, then click Run workflow in the Actions tab to trigger the agent](images/07a-workflow-dispatch-trigger.svg)

### Run your first compile check

```bash
gh aw compile
```

Expected result:

You see a green success message and a generated `daily-report-status.lock.yml` file next to `daily-report-status.md`. Open it to see the cron expression that `gh aw compile` created for `schedule: daily`.

> [!TIP]
> If you hit an error, see [Side Quest: Using `gh aw compile` to Catch Errors Early](side-quest-07-01-compile-workflow.md).

## ✅ Checkpoint

- [ ] `.github/workflows/daily-report-status.md` exists with `name`, `schedule: daily`, and `workflow_dispatch` in frontmatter
- [ ] You ran `gh aw compile` successfully
- [ ] `daily-report-status.lock.yml` was generated alongside your `.md` file
- [ ] The lock file contains a `cron:` expression (check with `cat .github/workflows/daily-report-status.lock.yml`)

<!-- journey: terminal -->
**Next:** [Part 2: Add instructions, safe outputs, and finish](07a-part2-your-first-workflow-instructions.md)
<!-- /journey -->

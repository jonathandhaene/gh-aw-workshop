<!-- page-journey: all -->
<!-- page-adventure: core -->
# Put Your Workflow on a Schedule

> _Turning your workflow into a hands-free, fully automated job is the payoff for everything you've built so far._

## 🎯 What You'll Do

You'll configure your workflow to run automatically on a recurring schedule — no manual trigger needed. By the end of this step, your daily status report will run on its own, every day, without you.

## 📋 Before You Start

- You have a working, tested workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- Your workflow file (e.g. `.github/workflows/daily-report-status.md`) is committed and pushed to GitHub.

## How Scheduled Agentic Workflows Work

Agentic workflows use the same `on:` trigger block as standard GitHub Actions. To run on a schedule, you add a `schedule:` entry with a fuzzy expression — no cron syntax required.

The `gh-aw` engine compiles your fuzzy expression (like `daily`) into a standard cron string in the `.lock.yml` file. You write something readable; the compiler handles the rest.

> [!NOTE]
> Scheduled workflows only run on the **default branch** of your repository (usually `main`). If your workflow file is on a different branch, the schedule won't activate until you merge.

## Add the Schedule Trigger

Open your workflow file (e.g. `.github/workflows/daily-report-status.md`) and find the `on:` block in the frontmatter. Add `schedule: daily` alongside your existing `workflow_dispatch:` trigger so you can still run it manually too.

```yaml
---
on:
  workflow_dispatch:
  schedule: daily
```

> [!TIP]
> Keep `workflow_dispatch:` while you're developing. It lets you trigger the workflow immediately without waiting for the schedule to fire.

<details>
<summary>Other schedule options</summary>

You can use any of the fuzzy schedule expressions supported by `gh-aw`:

| Expression | Runs |
|-----------|------|
| `daily` | Once per day (around midnight UTC) |
| `hourly` | Once per hour |
| `weekly` | Once per week (Mondays around midnight UTC) |

See [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) for the full list and how to choose the right cadence for your workflow.

</details>

## Compile and Push

After editing the frontmatter, you need to compile the workflow so the `.lock.yml` file is updated with the real cron schedule.

<details>
<summary>🖥️ GitHub UI alternative (no terminal needed)</summary>

If you are on the browser-only path, you can skip local compilation. GitHub will show an error on the Actions page if the compiled file is out of date — that's your signal to compile. Ask Copilot in the Agents tab:

```text
Using the agentic-workflows skill, compile daily-report-status and commit the lock file.
```

Then confirm the workflow file and `.lock.yml` are both present in `.github/workflows/` before moving on.

</details>

In a terminal or Codespace, run:

```bash
gh aw compile
```

This regenerates `.github/workflows/daily-report-status.lock.yml`. Commit **both** the `.md` file and the updated `.lock.yml`:

```bash
git add .github/workflows/daily-report-status.md \
        .github/workflows/daily-report-status.lock.yml
git commit -m "Add daily schedule trigger to status workflow"
git push
```

![Actions tab showing the next scheduled run time](images/13-scheduled-run.png)

## Confirm the Schedule Is Active

After pushing, open the **Actions** tab in your repository on GitHub. Select your workflow from the left-hand list. You should see:

- A badge showing **"scheduled"** in the trigger column for the next pending run.
- The previous manual runs listed below it.

If you don't see a scheduled run appear within a few minutes, check:

1. The workflow file is on the **default branch** (`main`).
2. The `.lock.yml` file was regenerated and pushed at the same time.
3. The repository has had at least one commit in the last 60 days (GitHub pauses scheduled workflows on inactive repositories).

> [!NOTE]
> GitHub may delay a scheduled workflow by up to 15 minutes during busy periods. This is expected — it doesn't mean something is wrong.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes `schedule: daily` (or another fuzzy expression)
- [ ] `gh aw compile` completed without errors (or Copilot compiled it for you)
- [ ] Both `daily-report-status.md` and the updated `daily-report-status.lock.yml` are committed and pushed to the default branch
- [ ] The Actions tab shows a pending or completed scheduled run for your workflow
- [ ] You can explain the difference between `workflow_dispatch` and `schedule` triggers

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

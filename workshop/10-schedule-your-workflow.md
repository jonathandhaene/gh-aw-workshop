<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule Your Workflow to Run Automatically

> _A workflow that runs on its own schedule turns a one-off experiment into a reliable automated assistant._

## 🎯 What You'll Do

You'll add a schedule trigger to your `daily-report-status` workflow so it runs once a day without any manual input. By the end, your workflow will be committed, compiled, and ready to fire automatically — giving you a live daily report every morning.

## 📋 Before You Start

- You have a working `daily-report-status` workflow from [Refine Your Workflow with Agentic Editing](09-agentic-editing.md).
- Both `.github/workflows/daily-report-status.md` and its `.lock.yml` are committed and pushed.

## Add the Schedule Trigger

Open `.github/workflows/daily-report-status.md` and look at the frontmatter at the top of the file — the section between the `---` fences.

You should already have a line like:

```yaml
on:
  workflow_dispatch:
```

Add a schedule entry below it:

```yaml
on:
  workflow_dispatch:
  schedule: daily
```

`schedule: daily` tells `gh aw compile` to create a once-a-day trigger. The compiler translates this fuzzy expression into a cron value in the `.lock.yml` file — you never need to write cron syntax yourself.

> [!TIP]
> Want a different cadence? Use `schedule: daily on weekdays`, `schedule: weekly`, or `schedule: every 6 hours`. See the [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) side quest for the full list.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-report-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `schedule: daily` under the `on:` block as shown above.
4. Click **Commit changes** and commit directly to your branch.

After committing in the UI, continue below from the **Recompile the workflow** section — you can rely on GitHub Actions feedback instead of running `gh aw compile` locally.

</details>

## Recompile the Workflow

Whenever you change the frontmatter, recompile so the `.lock.yml` reflects the new trigger:

```bash
gh aw compile
```

Check that the updated `.lock.yml` now includes a `schedule:` entry. You do not need to read the full file — look for the `on.schedule.cron` key near the top.

## Commit and Push Both Files

Both the `.md` source and the compiled `.lock.yml` must be pushed together:

```bash
git add .github/workflows/daily-report-status.md .github/workflows/daily-report-status.lock.yml
git commit -m "add daily schedule trigger to daily-report-status workflow"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

If you edited in the GitHub UI and committed there, the `.md` file is already saved. The compiled `.lock.yml` update is handled automatically when `gh aw` processes the workflow in GitHub Actions — no local compile step is required to continue.

</details>

## Verify the Trigger in GitHub Actions

1. Go to the **Actions** tab in your repository.
2. Select your **daily-report-status** workflow from the left sidebar.
3. Click **Run workflow** → **Run workflow** to trigger it manually one more time and confirm it still works.

The scheduled run will appear in the same list automatically the next day. GitHub shows scheduled runs with a clock icon next to the trigger name.

> [!NOTE]
> Scheduled workflows only run on the **default branch** (usually `main`). If your workflow file is on another branch, the schedule will not fire until that branch is merged. This applies to both `github.com` and GitHub Enterprise Server.

![Actions tab showing a scheduled workflow run with a clock icon](images/10-schedule-trigger.svg)

## ✅ Checkpoint

- [ ] `schedule: daily` is present in the frontmatter of `daily-report-status.md`
- [ ] `gh aw compile` completed without errors (or you committed via the UI and will rely on Actions feedback)
- [ ] Both `daily-report-status.md` and `daily-report-status.lock.yml` are committed and pushed
- [ ] A manual workflow run from the **Actions** tab completed successfully
- [ ] You can explain the difference between `workflow_dispatch` (manual) and `schedule: daily` (automatic)
- [ ] You know that scheduled workflows only run on the default branch

**Next:** [Test and Improve Your Workflow](12-test-and-iterate.md)

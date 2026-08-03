<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule It to Run Every Day

> _A workflow that only runs when you remember to trigger it isn't really automated — adding a schedule makes it work for you around the clock._

## 🎯 What You'll Do

You'll add a plain-English schedule trigger to your daily-status workflow so that GitHub Actions runs it automatically every weekday morning. By the end, your workflow will fire on its own without any manual intervention.

## 📋 Before You Start

- You have a working, tested workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- Both your workflow `.md` file and its compiled `.lock.yml` are committed and pushed.
- You can edit files in your Codespace, a local terminal, or the GitHub UI.

## Steps

### Add the schedule to your workflow frontmatter

Open your workflow `.md` file — for example, `daily-report-status.md`. Find the frontmatter block between the `---` fences at the top and add a `schedule` line:

```yaml
---
name: Daily Status Report
on:
  workflow_dispatch:
schedule: daily on weekdays
permissions:
  issues: write
---
```

Keep `workflow_dispatch` alongside the schedule. It lets you trigger the workflow manually at any time, which is essential for testing without waiting overnight.

> [!TIP]
> `gh-aw` accepts plain-English schedule expressions like `daily`, `daily on weekdays`, `weekly`, or `every 6 hours`. You do **not** write cron syntax here — `gh aw compile` converts your expression into the right cron value automatically. See [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) if you want to understand the mapping in detail.

### Recompile the workflow

Whenever you change frontmatter, you must regenerate the lock file before GitHub Actions can pick up the change.

```bash
gh aw compile
```

Open the generated `.lock.yml` file and find the `schedule` block. You'll see a cron expression that matches your fuzzy schedule — for example, `0 9 * * 1-5`. This is the value GitHub Actions actually uses.

> [!NOTE]
> The compiled cron value may be scattered to a slightly different time than you might expect. This is intentional — `gh-aw` staggers workflows across the hour to avoid peak load. Your lock file is the source of truth.

<details>
<summary>🖥️ GitHub UI alternative</summary>

If you are on a browser-only path or would prefer to skip the local compile step:

1. Navigate to your workflow `.md` file on GitHub and click the **pencil icon (✏️)** to edit it.
2. Add the `schedule` line to the frontmatter, then click **Commit changes**.
3. GitHub Actions will pick up the change on the next push — the `gh aw compile` step in your Actions workflow will regenerate the lock file automatically if you have set it up, otherwise trigger a `workflow_dispatch` run manually to validate the file first.

</details>

### Commit and push both files

You must commit the compiled lock file alongside the workflow definition. Both files need to be in sync for scheduled runs to work.

```bash
git add daily-report-status.md daily-report-status.lock.yml
git commit -m "Add daily weekday schedule to status workflow"
git push
```

### Verify the schedule is registered

Go to the **Actions** tab in your repository on GitHub. Select your workflow from the left sidebar. You should see the next scheduled run displayed near the top of the page.

> [!NOTE]
> GitHub may take up to a few minutes to register a new schedule after a push. If you do not see a scheduled run listed immediately, wait two minutes and refresh.

![Scheduled workflow showing the next run time in the Actions tab](images/13-scheduled-workflow-next-run.png)

### Trigger a manual test run

Don't wait until tomorrow to confirm the schedule works. Use `workflow_dispatch` to run it now:

1. In the **Actions** tab, select your workflow.
2. Click **Run workflow** → **Run workflow**.
3. Watch the run complete and confirm the output is still correct.

If the run succeeds manually, the scheduled runs will work the same way.

## ✅ Checkpoint

- [ ] You added `schedule: daily on weekdays` to your workflow frontmatter
- [ ] You ran `gh aw compile` (or confirmed the lock file was regenerated via Actions)
- [ ] You committed and pushed both the `.md` file and the `.lock.yml` file
- [ ] You can see the next scheduled run listed in the **Actions** tab
- [ ] You triggered a manual `workflow_dispatch` run and it completed successfully

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

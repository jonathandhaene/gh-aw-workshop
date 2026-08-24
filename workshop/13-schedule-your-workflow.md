<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule It to Run Every Day

> _A workflow that only runs when you remember to start it isn't really automated — scheduling turns your workflow into a reliable, hands-free assistant._

## 🎯 What You'll Do

You'll add a schedule trigger to your agentic workflow so it runs automatically at a time you choose. You'll use `gh-aw`'s plain-English schedule syntax, verify the compiled cron expression, and confirm your first automated run completes in the Actions tab.

## 📋 Before You Start

- You have a working workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- Your workflow file (e.g., `.github/workflows/daily-report-status.md`) is committed and compiles without errors.
- You have write access to your repository.

## Add a Schedule Trigger

Open your workflow source file — for example, `.github/workflows/daily-report-status.md`. Find the `on:` block in the YAML frontmatter at the top of the file. It probably looks something like this:

```yaml
on:
  workflow_dispatch:
```

Add a schedule entry so the workflow runs once a day on weekdays:

```yaml
on:
  workflow_dispatch:
  schedule: daily on weekdays
```

`gh-aw` compiles plain-English schedule phrases like `daily on weekdays` into standard GitHub Actions cron expressions. This keeps the source file readable while staying fully compatible with GitHub Actions.

> [!TIP]
> Common phrases: `daily`, `daily at 9am`, `every monday`, `weekly on friday at 5pm`, `hourly`. For a full reference, see [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md).

GitHub Actions runs scheduled workflows in **UTC**. If `9am` matters to your team, calculate the UTC equivalent first.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow file in your repository.
2. Click the **pencil icon (✏️)** to edit it.
3. Add the `schedule:` line to the frontmatter's `on:` block as shown above.
4. Click **Commit changes** to save.

You won't be able to run `gh aw compile` from the browser, so skip straight to "Commit and Push" below and let GitHub Actions validate the lock file on its next run. Any compile errors will appear in the Actions tab.

</details>

## Compile and Verify the Schedule

After saving the file, run:

```bash
gh aw compile
```

Open the compiled lock file (e.g., `.github/workflows/daily-report-status.lock.yml`) and look for an `on.schedule` block. It should contain the cron expression that matches your phrase:

```yaml
on:
  workflow_dispatch: {}
  schedule:
    - cron: "0 9 * * 1-5"
```

If the cron expression looks wrong, adjust the plain-English phrase and recompile.

## Commit and Push Both Files

Stage both the source file and the regenerated lock file:

```bash
git add .github/workflows/daily-report-status.md \
        .github/workflows/daily-report-status.lock.yml
git commit -m "add daily schedule trigger to daily-report-status workflow"
git push
```

GitHub Actions reads the lock file, not the source `.md` file. Both must be committed and pushed for the schedule to take effect.

## Verify the Schedule Is Active

1. Go to your repository's **Actions** tab.
2. Find your workflow in the left sidebar and click it.
3. Check that the workflow listing shows the schedule next to the trigger.

To test immediately without waiting for the scheduled time, click **Run workflow** and choose **workflow_dispatch** to start a manual run. Confirm it completes successfully.

> [!NOTE]
> GitHub Actions may delay the first run of a new schedule by up to 10 minutes. If your workflow doesn't appear on time, check that the repository has had at least one commit recently — GitHub sometimes pauses schedules on inactive repositories.

## ✅ Checkpoint

- [ ] You added a `schedule:` trigger to your workflow's frontmatter `on:` block
- [ ] `gh aw compile` completed without errors
- [ ] The compiled lock file contains an `on.schedule.cron` value matching your phrase
- [ ] You committed and pushed both the `.md` source file and the `.lock.yml` lock file
- [ ] The workflow appears under the scheduled-workflow listing in the **Actions** tab
- [ ] You triggered at least one successful manual run after adding the schedule

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

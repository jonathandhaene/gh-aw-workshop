<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule Your Workflow to Run Automatically

> _A workflow that only runs when you remember to trigger it isn't truly automated — adding a schedule is what makes it work for you._

## 🎯 What You'll Do

You'll add a `schedule` trigger to your workflow so GitHub Actions runs it automatically on a recurring cadence. By the end of this step, your workflow will fire on its own — no manual trigger needed.

## 📋 Before You Start

- You have a working workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- Your workflow file is committed and pushed to your GitHub repository.
- You can edit files in the GitHub UI or in your terminal/Codespace.

## Add a Schedule Trigger

Open your workflow file (for example, `daily-report-status.md`). Find the frontmatter section between the opening and closing `---` fences at the top of the file.

Look for the `on:` field. It probably says `workflow_dispatch` — meaning it only runs when you click the button. You're going to add a schedule alongside it.

Replace (or extend) the `on:` field with both triggers:

```yaml
on:
  workflow_dispatch:
  schedule: daily on weekdays
```

The `schedule: daily on weekdays` line tells `gh-aw` to run the workflow once a day, Monday through Friday. You can use plain English — `gh aw compile` will convert it to the correct GitHub Actions cron expression.

> [!TIP]
> Not sure which schedule fits your workflow? See [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) for a full reference table.

### Edit in the GitHub UI

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow file in your repository on GitHub.
2. Click the **pencil icon (✏️)** to edit it.
3. Add the `schedule:` line to the `on:` section as shown above.
4. Click **Commit changes** and commit directly to your default branch.

</details>

## Compile the Updated Workflow

After saving your edit, regenerate the lock file so GitHub Actions picks up the new cron schedule:

```bash
gh aw compile
```

The compiled output will show the cron expression `gh-aw` generated from your plain-English schedule — something like `50 11 * * 1-5` for `daily on weekdays`. That's the value that actually runs in GitHub Actions.

Commit both the `.md` file and the updated `.lock.yml`:

```bash
git add daily-report-status.md daily-report-status.lock.yml
git commit -m "Add daily schedule trigger"
git push
```

> [!NOTE]
> If you're working in the GitHub UI and don't have `gh aw compile` available locally, push your change and let GitHub Actions compile it automatically on the next run. The compilation step is part of the workflow's setup — the schedule will appear after the first successful compile run.

## Verify the Schedule Appears in GitHub Actions

After pushing:

1. Open your repository on GitHub and click the **Actions** tab.
2. Click your workflow name in the left sidebar.
3. Look for the **Scheduled** event type in the workflow summary. It may say "This workflow has a `schedule` event trigger."

GitHub Actions may take a few minutes to register a new schedule. If you don't see it immediately, wait a minute and refresh.

![Schedule trigger visible in the Actions workflow summary](images/13-schedule-trigger.png)

## Trigger a Manual Run to Confirm Everything Still Works

Before waiting for the schedule to fire on its own, verify nothing broke:

1. Click **Run workflow** → **Run workflow** in the Actions UI.
2. Watch the run complete successfully.
3. Confirm the output looks the same as before you added the schedule.

If the manual run succeeds, your scheduled workflow is ready.

## ✅ Checkpoint

- [ ] You added `schedule: daily on weekdays` (or your chosen expression) to the `on:` section of your workflow frontmatter
- [ ] You kept `workflow_dispatch` in the `on:` section so you can still trigger runs manually
- [ ] You ran `gh aw compile` and committed both the `.md` file and the updated `.lock.yml`
- [ ] You triggered a manual run and it completed successfully
- [ ] You can see the scheduled trigger registered in the GitHub Actions workflow summary

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

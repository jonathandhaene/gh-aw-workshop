<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule Your Workflow to Run Every Day

> _A workflow that runs itself is worth a hundred workflows you have to remember to run._

## 🎯 What You'll Do

You'll add a `schedule:` trigger to `daily-report-status.md` so GitHub Actions runs it automatically — no button click required. By the end of this step, your daily status report will run on its own every day and you'll understand how to adjust the schedule at any time.

## 📋 Before You Start

- You completed [Test and Improve Your Workflow](12-test-and-iterate.md) and have a working `daily-report-status.md`.
- Your workflow compiles cleanly with `gh aw compile` and at least one manual run succeeded.
- Your compiled `daily-report-status.lock.yml` is committed and pushed to your repository.

## Add a Schedule Trigger

Open `daily-report-status.md` in your editor. Find the `on:` block near the top of the frontmatter — it currently looks something like this:

```yaml
on:
  workflow_dispatch:
```

Add the `schedule:` line directly below `workflow_dispatch:`:

```yaml
on:
  workflow_dispatch:
  schedule: daily on weekdays
```

The `schedule: daily on weekdays` expression tells `gh-aw` to run your workflow once a day, Monday through Friday. `gh aw compile` converts it to a cron expression automatically — you never need to write `0 9 * * 1-5` by hand.

> [!TIP]
> Want a different cadence? You can write `schedule: daily`, `schedule: weekly`, or even `schedule: every 6 hours`. See the [Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) side quest for the full reference.

## Compile and Commit

After saving the file, recompile to update the lock file:

```bash
gh aw compile
```

You should see output confirming the schedule compiled to a cron expression. Check the compiled value in `daily-report-status.lock.yml`:

```bash
grep -A 3 "schedule:" daily-report-status.lock.yml
```

Now commit both files:

```bash
git add .github/workflows/daily-report-status.md .github/workflows/daily-report-status.lock.yml
git commit -m "Add daily schedule trigger to daily-report-status"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/daily-report-status.md` in your repository and click the **pencil icon (✏️)**.
2. Add `schedule: daily on weekdays` to the `on:` block as shown above.
3. Click **Commit changes**.
4. Because UI edits skip the local compile step, GitHub Actions will run `gh aw compile` automatically on the pushed commit. Watch the **Actions** tab — a compile-and-lock run should appear within a minute.

</details>

## Verify the Schedule Is Registered

After pushing, GitHub needs to register the new schedule. This can take a few minutes.

1. Go to your repository on GitHub.
2. Click the **Actions** tab.
3. Select **daily-report-status** from the workflow list on the left.
4. Look for **Scheduled** next to the workflow name — this confirms GitHub has picked up the cron trigger.

![Actions tab showing a scheduled workflow with the next run time displayed](images/13-schedule-registered.png)

> [!NOTE]
> A scheduled workflow only runs on branches that contain the trigger definition in the default branch (usually `main`). If you are working on a feature branch, push the schedule change to `main` before expecting automated runs.

## What Happens Next

From now on, your workflow runs automatically at the compiled schedule. You can still trigger it manually at any time from the **Actions** tab — the `workflow_dispatch:` trigger stays in place alongside the schedule.

If you want to pause the automatic runs temporarily, go to **Actions** → select the workflow → click **···** → **Disable workflow**. Re-enable it the same way when you're ready.

## ✅ Checkpoint

- [ ] You added `schedule: daily on weekdays` (or your chosen expression) to the `on:` block in `daily-report-status.md`
- [ ] `gh aw compile` completed without errors and updated `daily-report-status.lock.yml`
- [ ] Both `.md` and `.lock.yml` are committed and pushed to `main`
- [ ] The **Actions** tab shows your workflow with a **Scheduled** indicator
- [ ] You can explain the difference between `workflow_dispatch:` and `schedule:` triggers

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

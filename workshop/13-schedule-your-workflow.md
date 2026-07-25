<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule It to Run Every Day

> _A workflow that only runs when you remember to click a button isn't an automation — adding a schedule makes it truly hands-free._

## 🎯 What You'll Do

You'll update your `daily-report-status` workflow to run automatically every day at a time you choose. By the end of this step, GitHub Actions will trigger your AI-powered report on a schedule — without you lifting a finger.

## 📋 Before You Start

- You completed [Test and Improve Your Workflow](12-test-and-iterate.md) and have a working workflow with output you are happy with.
- Your workflow `.md` file is committed and pushed to the default branch.

## Add the Schedule Trigger

Open `.github/workflows/daily-report-status.md` in your editor and look at the frontmatter at the top of the file. It currently looks something like this:

```yaml
---
name: Daily Report Status
on:
  workflow_dispatch:
---
```

Add a `schedule` field so the workflow also runs automatically:

```yaml
---
name: Daily Report Status
on:
  workflow_dispatch:
  schedule: daily on weekdays
---
```

`schedule: daily on weekdays` is a plain-English **fuzzy expression** — `gh aw compile` converts it to a standard GitHub Actions cron expression for you. You do not need to write cron by hand.

> [!TIP]
> Other useful fuzzy expressions: `schedule: daily`, `schedule: weekly`, `schedule: every 6 hours`. See [Side Quest: Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) for the full list and their compiled cron equivalents.

<details>
<summary>🖥️ Editing in the GitHub UI</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-report-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `schedule: daily on weekdays` line to the `on:` block as shown above.
4. Click **Commit changes** — but **do not push yet**. You still need to compile and commit the lock file before the schedule takes effect.

If you are editing in the UI and cannot run `gh aw compile` locally, open a Codespace (see [Set Up a GitHub Codespace](02a-setup-codespace.md)) or use the Copilot Agents tab to compile for you.

</details>

## Compile the Updated Workflow

After saving the source file, regenerate the lock file so GitHub Actions picks up the new trigger:

```bash
gh aw compile
```

Check the output. You should see a `schedule:` block appear in the compiled `.lock.yml` file, containing a standard cron expression like `0 12 * * 1-5`.

If the compiler reports an error, re-read the frontmatter for typos — YAML is whitespace-sensitive, and `schedule:` must be indented at the same level as `workflow_dispatch:`.

## Commit and Push Both Files

GitHub Actions only sees the compiled lock file, so commit both the source and the lock file together:

```bash
git add .github/workflows/daily-report-status.md .github/workflows/daily-report-status.lock.yml
git commit -m "add daily schedule trigger"
git push
```

<details>
<summary>🖥️ GitHub UI alternative for the commit</summary>

If you edited the source file in the UI, you need to also commit the compiled lock file. The easiest approach is to:

1. Open a Codespace or terminal in your repository.
2. Pull the latest changes: `git pull`.
3. Run `gh aw compile`, then commit and push both files as shown above.

Alternatively, ask the Copilot Agents tab: _"Compile the `daily-report-status` workflow and commit both files."_

</details>

## Verify the Schedule in the Actions Tab

After pushing, open your repository on GitHub and click the **Actions** tab. Select the **Daily Report Status** workflow from the left sidebar.

You should see a "This scheduled workflow will run next at…" notice near the top of the page.

![The Actions tab showing the next scheduled run time for the Daily Report Status workflow](images/13-scheduled-next-run.png)

> [!NOTE]
> GitHub Actions schedules run in UTC. If you want the report to arrive at 9 AM in your time zone, adjust the schedule expression accordingly — for example, `schedule: daily at 14:00` for UTC+5. See the [Fuzzy Schedule Expressions side quest](side-quest-13-01-schedule-expressions.md) for time-zone guidance.

## Trigger a Test Run Right Now

Don't wait a full day to confirm the workflow works. Trigger it manually from the same **Actions** tab:

1. Click **Run workflow** → **Run workflow**.
2. Wait for the run to complete — it usually takes under two minutes.
3. Open the run and check the output looks correct.

Once a manual trigger succeeds with the new schedule field in place, you can be confident the automatic daily run will work as well.

## ✅ Checkpoint

- [ ] I added a `schedule:` field to the `on:` block in my workflow frontmatter
- [ ] I ran `gh aw compile` and saw a cron expression in the updated lock file
- [ ] I committed and pushed both the `.md` source file and the `.lock.yml` lock file
- [ ] The **Actions** tab shows a scheduled next-run notice for my workflow
- [ ] I triggered a manual run and confirmed the output is correct

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

<!-- page-journey: all -->
<!-- page-adventure: core -->
# Schedule Your Workflow to Run Every Day

> _A workflow that runs itself is more valuable than one you remember to trigger._

## 🎯 What You'll Do

You'll add a `schedule:` trigger to your daily-status workflow so GitHub Actions fires it automatically on a recurring schedule. By the end, your workflow will run on its own — no manual trigger needed.

## 📋 Before You Start

- Completed [Test and Improve Your Workflow](12-test-and-iterate.md)
- Your workflow runs successfully when triggered manually
- You know where your workflow `.md` file lives in the repository

## Add a schedule trigger

Open your workflow file (e.g., `daily-status.md`) in the GitHub editor. Find the `on:` section in the YAML frontmatter.

It currently looks something like this:

```yaml
on:
  workflow_dispatch:
```

Add a `schedule:` entry below it. You can write a plain-English expression — `gh-aw` will compile it into the correct cron format:

```yaml
on:
  workflow_dispatch:
  schedule: daily
```

> [!TIP]
> `daily` compiles to a fixed time each day (UTC). Use `daily on weekdays` if you want the workflow to stay quiet on weekends, or `every 6 hours` for more frequent runs. See the [Side Quest: Fuzzy Schedule Expressions](side-quest-13-01-schedule-expressions.md) reference for the full list.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow file in the repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `schedule: daily` under the `on:` block as shown above.
4. Click **Commit changes**, add a short message like `Add daily schedule trigger`, and commit directly to your default branch.

</details>

## Compile and push the change

After editing, regenerate the lock file so GitHub Actions picks up the compiled cron expression:

```bash
gh aw compile daily-status
```

Then commit both the updated `.md` file and the regenerated `.lock.yml`:

```bash
git add daily-status.md daily-status.lock.yml
git commit -m "Add daily schedule trigger"
git push
```

> [!NOTE]
> GitHub Actions only activates a `schedule:` trigger after the workflow file is on the **default branch**. If you push to a feature branch, the schedule will not fire until it is merged.

## Verify the schedule was registered

Once your push lands on the default branch, go to the **Actions** tab in your repository. Click your workflow name in the left sidebar. You should see **Schedule** listed under **Triggers** on the workflow summary page.

![Actions tab showing the daily-status workflow with Schedule listed under Triggers](images/13-schedule-trigger.png)

If you don't see **Schedule** listed yet, wait a minute and refresh — GitHub may take up to a few minutes to register a new schedule.

## Watch your first automatic run

The schedule will fire at the next occurrence of the compiled cron time. You don't need to do anything — GitHub Actions will call your workflow automatically.

To check when the next run is due, look at the compiled cron value in your `.lock.yml` file:

```bash
grep "cron:" daily-status.lock.yml
```

Use a tool like [crontab.guru](https://crontab.guru) to convert the cron expression into a human-readable time.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `schedule:` entry (e.g., `schedule: daily`)
- [ ] You ran `gh aw compile` after adding the trigger
- [ ] Both the `.md` file and the `.lock.yml` file are committed and pushed to the default branch
- [ ] The **Actions** tab shows **Schedule** as a trigger for your workflow
- [ ] You can read the compiled `cron:` value from the lock file and explain when the next run will fire

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

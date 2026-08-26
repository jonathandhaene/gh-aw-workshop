<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Chain Multiple Conditions for Smarter Scheduling

> _One condition keeps your workflow focused — chained conditions make it truly intelligent about when to run._

## 🎯 What You'll Do

Extend the commit-count guard you added in the previous step with a day-of-week check. When both conditions are combined, your workflow skips execution on weekends _and_ on quiet weekdays with no commits. You'll practice using `&&` to compose `if:` expressions from multiple step outputs.

## 📋 Before You Start

- You have completed [Make Your Workflow Smarter with Conditional Logic](15-conditional-logic.md).
- Your workflow already has a `Count recent commits` step with `id: recent` and an `if:` key in the frontmatter.
- You can open and edit your workflow file — either in the GitHub UI or in your Codespace/local terminal.

## Steps

### Understand the goal

Your workflow now skips execution when there are no recent commits. The next refinement is to also skip weekends, because your team doesn't expect a status report on Saturday or Sunday. You'll do this by adding a second step that captures the current day name, then updating the `if:` condition to require _both_ checks to pass before the agent job runs.

### Add a day-of-week step

Open your workflow file and add this step immediately after the `Count recent commits` step, inside the YAML frontmatter `steps:` block:

```yaml
- name: Check day of week
  id: day
  run: echo "day=$(date +%A)" >> $GITHUB_OUTPUT
```

The `date +%A` command returns the full weekday name for the current runner locale — for example, `Monday`, `Saturday`, or `Sunday`. Writing it to `$GITHUB_OUTPUT` with `id: day` makes it available as `steps.day.outputs.day` anywhere in the same job context.

### Update the `if:` condition

Locate the top-level `if:` key in your frontmatter and replace it with the combined expression:

```yaml
if: steps.recent.outputs.commit_count != '0' && steps.day.outputs.day != 'Saturday' && steps.day.outputs.day != 'Sunday'
```

The `&&` operator short-circuits: GitHub Actions evaluates left to right and skips the agent job as soon as any clause is false. This means on a Saturday the job is skipped before even checking the commit count.

> [!TIP]
> Keep long `if:` expressions on a single line in the frontmatter. The YAML block scalar syntax (`>-`) does not work inside frontmatter `if:` keys, so line-continuation tricks are not available here.

### Compile and push your changes

After editing, regenerate the lock file so the updated condition is embedded in the compiled job definition.

**Terminal path:**

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "feat: skip summary on weekends and quiet days"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Make your changes to the frontmatter.
4. Click **Commit changes**.
5. Then open your Codespace or local terminal and run `gh aw compile`, and push the updated `.lock.yml`. The condition does not take effect until the compiled file is pushed.

</details>

### Test the condition manually

Trigger a manual run from the Actions tab to verify the combined logic:

1. In your repository, click the **Actions** tab.
2. Select your daily-status workflow from the left sidebar.
3. Click **Run workflow**.
4. Watch the run: on a weekday with commits the agent job should complete normally. On a weekend (or if you temporarily set the commit count to `'0'` in the step) the job should appear as **skipped** with a grey icon.

> [!NOTE]
> You can simulate a weekend skip by temporarily changing `date +%A` to `echo Saturday` in the day step, then compiling and re-running. Remember to revert after testing.

## ✅ Checkpoint

- [ ] Your workflow frontmatter has a second step with `id: day` that writes the day name to `$GITHUB_OUTPUT`
- [ ] The `if:` condition combines both `commit_count != '0'` and the two weekend day checks with `&&`
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] You triggered the workflow manually and can see either a completed run or a skipped run, matching the current day and commit activity
- [ ] You understand why `&&` short-circuits and what happens if the first clause is false

**Next:** [Connect a Live Data Source to Your Workflow](16-connect-data-source.md)

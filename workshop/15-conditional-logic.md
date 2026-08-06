<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Make Your Workflow Smarter with Conditional Logic

> _A workflow that always runs is useful — a workflow that only runs when it matters is elegant._

## 🎯 What You'll Do

Add a conditional check to your daily-status workflow so it only posts a summary when there have been recent commits. You'll learn how to use shell commands to gather context, expose that context as step outputs, and wire it into an `if:` condition that short-circuits the agent job entirely on quiet days.

## 📋 Before You Start

- You have a working daily-status workflow from [Build: Daily Repo Status Workflow](07-your-first-workflow.md).
- You understand how to edit and re-run a workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).

## Steps

### Understand the problem

Your daily-status workflow currently runs every weekday regardless of repository activity, which means it can produce empty or near-empty summaries like "No activity to report" on quiet days. Over time these hollow reports erode confidence in the tool because readers learn to ignore them.

The fix has two parts you'll tackle one at a time:

1. **Add a commit-count step** — a deterministic shell step that inspects repository state and writes a value to `$GITHUB_OUTPUT`.
2. **Wire the condition into frontmatter** — an `if:` key that skips the agent job entirely when the commit count is zero.

---

### Part A — Add a commit-count step

Open your daily-status workflow file (e.g., `.github/workflows/daily-status.md`) and add the following block inside the YAML frontmatter under `steps:`:

```yaml
steps:
  - name: Count recent commits
    id: recent
    run: |
      COUNT=$(git log --oneline --since="24 hours ago" | wc -l | tr -d ' ')
      echo "commit_count=$COUNT" >> $GITHUB_OUTPUT
```

`$GITHUB_OUTPUT` is a special GitHub Actions file that shares values between steps using `key=value` notation. The `id: recent` field names this step so its output is reachable as `steps.recent.outputs.commit_count` in later steps or in a top-level condition.

> [!NOTE]
> For a deeper look at the `steps` context and built-in expression functions like `contains()`, see [Side Quest: GitHub Actions Expressions and Contexts](side-quest-15-01-expressions-and-contexts.md).

**✋ Pause and verify** — compile now to confirm the new step is valid before you continue:

```bash
gh aw compile
```

You should see `✅ Compiled successfully`. If you see an error, check that the `id: recent` field is indented correctly under `steps:`.

---

### Part B — Wire the condition into frontmatter

Now add a top-level `if:` key at the same indentation level as `on:` and `steps:` in your frontmatter:

```yaml
if: steps.recent.outputs.commit_count != '0'
```

This condition is embedded into the generated lock file during [compilation](https://github.github.com/gh-aw/reference/compilation-process/). At runtime, GitHub Actions evaluates it and skips the agent job entirely whenever `commit_count` evaluates to `'0'`.

You can also reference the count inside your prompt to give the model concrete context — for example:

```
Summarise the last ${{ steps.recent.outputs.commit_count }} commits.
```

**✋ Pause and verify** — compile and trigger a manual run to confirm the condition works:

1. Run `gh aw compile` and confirm no errors.
2. Trigger a manual [`workflow_dispatch`](https://github.github.com/gh-aw/reference/triggers/) run from the Actions tab.
3. Inspect the run log. On a day with commits the agent job should complete normally. On a day with no commits it should appear **skipped** with a grey icon.

![Skipped step in GitHub Actions](images/15-skipped-step.svg)

---

### Exercise: Add a weekend skip condition

Now that the commit-count condition is in place, extend the workflow to also skip execution on weekends. This reinforces how to chain multiple conditions in a single `if:` expression.

1. Add a step that writes the current day name as an output:

```yaml
- name: Check day of week
  id: day
  run: echo "day=$(date +%A)" >> $GITHUB_OUTPUT
```

1. Update the top-level `if:` to combine both conditions using `&&`:

```yaml
if: steps.recent.outputs.commit_count != '0' && steps.day.outputs.day != 'Saturday' && steps.day.outputs.day != 'Sunday'
```

1. Compile and trigger another manual run. On a weekend the job should appear as **skipped** regardless of commit count.

### Commit and push your changes

**Terminal path:**

```bash
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "feat: skip summary on days with no commits"
git push
```

**GitHub UI path:**

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `steps:` block and `if:` field to the frontmatter, then click **Commit changes**.

> [!IMPORTANT]
> Committing the `.md` file via the web editor does **not** automatically recompile the lock file. After committing, open your Codespace or local terminal and run `gh aw compile`, then push the updated `.lock.yml`. The `if:` condition will not take effect until the compiled lock file is pushed.

## ✅ Checkpoint

- [ ] Your workflow has a `count recent commits` step with `id: recent`
- [ ] Running `gh aw compile` after Part A completed without errors
- [ ] Your workflow frontmatter includes `if: steps.recent.outputs.commit_count != '0'`
- [ ] You triggered a manual run after Part B and confirmed the conditional behaviour in the run log
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] Both `.github/workflows/daily-status.md` and `.github/workflows/daily-status.lock.yml` are committed and pushed
- [ ] The workflow still posts a summary on days with commits

<!-- journey: all -->
**Next:** [Connect a Live Data Source to Your Workflow](16-connect-data-source.md)
<!-- /journey -->



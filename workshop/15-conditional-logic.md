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

Your daily-status workflow currently runs every weekday regardless of repository activity, which means it can produce empty or near-empty summaries like "No activity to report" on quiet days. Over time these hollow reports erode confidence in the tool because readers learn to ignore them. Conditional logic solves this by inspecting repository state in a deterministic shell step before any AI processing begins, then skipping the agent job entirely when the precondition is not met.

The approach breaks into three parts:
1. Run a shell command to count commits from the last 24 hours and write the result to `$GITHUB_OUTPUT`.
2. Reference that output using the `steps` context expression `${{ steps.recent.outputs.commit_count }}`.
3. Add a top-level `if:` key in the workflow [frontmatter](https://github.github.com/gh-aw/reference/frontmatter/) that skips the agent job when the count evaluates to zero.

### Write the shell command

Open your daily-status workflow file (e.g., `.github/workflows/daily-status.md`) and add the following block inside the YAML frontmatter under `steps:`:

```yaml
steps:
  - name: Count recent commits
    id: recent
    run: |
      COUNT=$(git log --oneline --since="24 hours ago" | wc -l | tr -d ' ')
      echo "commit_count=$COUNT" >> $GITHUB_OUTPUT
```

The `git log --since="24 hours ago"` command lists commits from the past day. Piping to `wc -l` counts those lines, and `tr -d ' '` trims whitespace to give a clean integer.

### Expose it as a step output

The last line in the `run:` block writes the count to `$GITHUB_OUTPUT`:

```bash
echo "commit_count=$COUNT" >> $GITHUB_OUTPUT
```

`$GITHUB_OUTPUT` is a special file GitHub Actions uses to share values between steps. Writing `key=value` to it makes the value available as `steps.<id>.outputs.key` in any later step or condition. Because the step above has `id: recent`, the count is accessible as `steps.recent.outputs.commit_count`.

> [!NOTE]
> For a deeper look at the `steps` context, expression functions like `contains()`, and condition chaining with `&&` / `||`, see [Side Quest: GitHub Actions Expressions and Contexts](side-quest-15-01-expressions-and-contexts.md).

### Add a top-level condition in frontmatter

In the same frontmatter block, add a top-level `if:` key at the same indentation level as `on:` and `steps:`:

```yaml
if: steps.recent.outputs.commit_count != '0'
```

At runtime, GitHub Actions evaluates this expression before running the agent job and skips it entirely when `commit_count` is `'0'`. You can also use the count inside your prompt text — for example: `"Summarise the last ${{ steps.recent.outputs.commit_count }} commits"` — to give the model concrete scope.

### Exercise: Add a weekend skip condition

Extend the workflow to also skip on weekends.

1. Add a step that writes the current day name as an output:

```yaml
- name: Check day of week
  id: day
  run: echo "day=$(date +%A)" >> $GITHUB_OUTPUT
```

1. Update the top-level `if:` to combine both conditions:

```yaml
if: steps.recent.outputs.commit_count != '0' && steps.day.outputs.day != 'Saturday' && steps.day.outputs.day != 'Sunday'
```

> [!NOTE]
> The `&&` operator chains conditions — the job runs only when all conditions are true. For more on condition chaining, see [Side Quest: GitHub Actions Expressions and Contexts](side-quest-15-01-expressions-and-contexts.md).

1. Compile the workflow with `gh aw compile` to regenerate the lock file.

1. Trigger a manual [`workflow_dispatch`](https://github.github.com/gh-aw/reference/triggers/) run from the Actions tab.

1. Inspect the run log: on a weekday with commits the agent job completes normally; on a weekend or a quiet day it shows as **skipped** with a grey icon.

![Skipped step in GitHub Actions](images/15-skipped-step.svg)

### Compile your changes

After editing the frontmatter, compile the workflow to confirm everything is valid:

```bash
gh aw compile
```

You should see `✅ Compiled successfully`. This regenerates your `.lock.yml` file with the updated conditional logic embedded in the job definition.

> [!NOTE]
> The `if:` condition is applied during [compilation](https://github.github.com/gh-aw/reference/compilation-process/) and will not take effect until you compile and push both the `.md` source and the updated `.lock.yml` file.

### Terminal path — Commit and push your conditional logic

```bash
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "feat: skip summary on days with no commits"
git push
```

### GitHub UI path — Commit and push your conditional logic

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `steps:` block and `if:` field to the frontmatter.
4. Click **Commit changes**.

> [!IMPORTANT]
> Committing the `.md` file via the web editor does **not** automatically recompile the lock file. After committing, open your Codespace or local terminal and run `gh aw compile`, then push the updated `.lock.yml`. The `if:` condition will not take effect until the compiled lock file is pushed.

## ✅ Checkpoint

- [ ] Your workflow has a `count recent commits` step with `id: recent`
- [ ] Your workflow frontmatter includes `if: steps.recent.outputs.commit_count != '0'`
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] Both `.github/workflows/daily-status.md` and `.github/workflows/daily-status.lock.yml` are committed and pushed
- [ ] You triggered the workflow manually and confirmed the conditional behaviour in the run log
- [ ] The workflow still posts a summary on days with commits

<!-- journey: all -->
**Next:** [Connect a Live Data Source to Your Workflow](16-connect-data-source.md)
<!-- /journey -->



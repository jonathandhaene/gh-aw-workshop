<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Prevent Race Conditions with Concurrency Controls

> _Two runs writing the same issue comment at the same time produce garbled output — concurrency controls stop that before it starts._

## 🎯 What You'll Do

You'll add a `concurrency:` block to your agentic workflow so that only one run executes at a time per branch or per repository. By the end, you'll understand how `cancel-in-progress` and concurrency groups protect against duplicate writes without blocking every run.

## 📋 Before You Start

- You completed [Make Your Workflows Resilient to Failure](22-error-handling-and-resilience.md).
- You understand the `safe-outputs:` section from [Write Your First Agentic Workflow](07-your-first-workflow.md).
- You have a workflow that writes output (comments, issue updates, or file commits).

## What is a Race Condition?

A race condition happens when two workflow runs start close together and both try to write the same output.

Imagine a `push` trigger. Two commits land 10 seconds apart. Both runs start, both read the same issue thread, both post a comment. The result: two nearly-identical comments, often confusing and sometimes contradictory.

The `concurrency:` block in your workflow frontmatter prevents this. It lets you say: "only one run for this group at a time."

## Steps

### Add a concurrency group

Open your workflow file and add a `concurrency:` block directly in the YAML frontmatter:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 9 * * *"
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: false

permissions:
  contents: read
  issues: write
---
```

This example uses `${{ github.workflow }}-${{ github.ref }}` as the group key. It means:

- All runs of this workflow on the same branch share one concurrency slot.
- A new run waits until the previous one finishes before it starts.

### Choose the right cancel strategy

The `cancel-in-progress` field controls what happens when a new run arrives while one is already running.

| Strategy | When to use it |
|----------|---------------|
| `cancel-in-progress: false` (default) | Queue the new run; finish the current one first. Good for report workflows where every run should complete. |
| `cancel-in-progress: true` | Cancel the current run immediately. Good for PR review workflows where only the latest state matters. |

For a scheduled daily-status report, `false` is the safer default — you want every run to complete and post its own output.

For a PR reviewer triggered on `pull_request`, `true` is usually better — a new commit invalidates the previous review anyway.

### Use a tighter group key for PR workflows

For PR-scoped workflows, narrow the group key to the pull request number so runs for different PRs can still run in parallel:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number }}
  cancel-in-progress: true
```

This limits queuing to a single PR, not the entire branch. Two open PRs can run their reviewers simultaneously.

### Compile and verify

After editing the frontmatter, compile to regenerate the lock file:

```bash
gh aw compile
```

Commit both files together:

```bash
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "Add concurrency controls to prevent duplicate writes"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow `.md` file on GitHub.
2. Click the **pencil icon (✏️)** to edit it.
3. Add the `concurrency:` block between `on:` and `permissions:` as shown above.
4. Click **Commit changes**.
5. You won't be able to run `gh aw compile` from the UI — the lock file will be regenerated automatically the next time the workflow is triggered.

</details>

### Test by triggering two rapid runs

You can simulate a race condition by triggering two manual runs in quick succession:

1. Go to **Actions** → your workflow → **Run workflow** and click **Run workflow**.
2. Wait two seconds, then trigger it again.

Open the **Actions** tab and watch. With `cancel-in-progress: false`, the second run shows **Waiting** until the first completes. The output from each run stays separate and uncorrupted.

> [!NOTE]
> Enterprise environments with high-frequency event triggers (busy PRs, push-heavy repos) benefit most from concurrency controls. Without them, multiple agents can write conflicting outputs to the same thread.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `concurrency:` block with a group key and a `cancel-in-progress` value
- [ ] You chose `cancel-in-progress: false` for a report-style workflow or `cancel-in-progress: true` for a PR-scoped workflow, and you can explain why
- [ ] You ran `gh aw compile` and committed both the `.md` and `.lock.yml` files
- [ ] You triggered two runs in quick succession and confirmed the second run waited or was cancelled as expected
- [ ] You can describe what a race condition looks like in the Actions log and how `concurrency:` prevents it

**Return to:** [Make Your Workflows Resilient to Failure](22-error-handling-and-resilience.md) or continue to [Test Your Prompt Ideas with A/B Experiments](23-ab-experiments.md)

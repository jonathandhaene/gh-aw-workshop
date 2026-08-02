<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Review Before You Write: Staged Mode

> _Staged mode gives you a human checkpoint between what your agent decides and what it actually changes — essential for teams that need an audit trail or can't afford a bad automated commit._

## 🎯 What You'll Do

Enable staged mode on an existing workflow so that every proposed write — comment, commit, issue, or label — pauses for your approval before it executes. You'll trigger a run, review the staged output in the Actions UI, and approve or reject it.

## 📋 Before You Start

- You have a working agentic workflow file, such as `daily-status.md`, from [Test and Improve Your Workflow](12-test-and-iterate.md).
- You understand how `safe-outputs` declarations gate what the agent can write, from [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).

## Steps

### Understand what staged mode does

In a normal run the agent plans, calls tools, and writes its safe outputs — all in one unattended pass. Staged mode splits that into two phases:

1. **Plan phase** — the agent runs and produces a _staged output bundle_ describing every write it wants to make.
2. **Approval phase** — a human reviews the bundle in the Actions UI and clicks **Approve** or **Reject** before any write executes.

Nothing is committed, commented, or labeled until you approve. If you reject, the run ends cleanly with no side effects.

> [!TIP]
> Staged mode is especially useful for workflows that write to production branches, post in public issue threads, or operate across many repositories where a rogue write would be hard to undo.

### Add `staged: true` to your workflow frontmatter

Open your workflow file (for example `daily-status.md`) and add one line inside the frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
  workflow_dispatch:
permissions:
  issues: write
staged: true
safe-outputs:
  - add-issue-comment
---
```

The `staged: true` field tells the gh-aw runtime to halt after the plan phase and wait for approval.

<details>
<summary>🖥️ GitHub UI path</summary>

1. In your repository, navigate to the workflow file (for example `.github/workflows/daily-status.md`).
2. Click the **pencil icon (✏️)** to open the editor.
3. Inside the opening `---` fence, add the line `staged: true` after the `permissions:` block.
4. Click **Commit changes**.

</details>

### Compile and push

If you are working in a terminal or Codespace, regenerate the lock file so the change takes effect:

```bash
gh aw compile daily-status
```

Then commit and push both files:

```bash
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "Enable staged mode on daily-status workflow"
git push
```

UI-first learners who committed the `staged: true` change directly via the GitHub editor can skip the compile step — the Actions runner will compile from source when the workflow next runs.

### Trigger a staged run

Go to **Actions** → **Daily Status Report** → **Run workflow** → **Run workflow** to start a manual run.

The run will complete the plan phase and then pause. In the job summary you will see a **Review staged output** banner. Click it to open the approval view.

![Staged output approval view in GitHub Actions](images/27-staged-mode-approval.png)

### Review and approve or reject

The approval view shows every write the agent intends to make — for example, the exact text of a comment it would post.

- Click **Approve** to execute all writes immediately.
- Click **Reject** to discard them. The run is marked as cancelled with no side effects.

> [!NOTE]
> Staged approvals time out after 30 days. If no one approves within that window the run expires and is treated as a rejection.

### Make staged approval required for a branch (optional, enterprise teams)

If your organisation uses branch protection rules, you can add the staged-output approval step as a required status check so no merge can proceed without a reviewer approving the proposed workflow writes. See the [gh-aw staged mode reference](https://github.github.com/gh-aw/reference/staged-mode/) for the required check name.

## ✅ Checkpoint

- [ ] You added `staged: true` to a workflow's frontmatter and compiled or committed the change
- [ ] You triggered a manual run and the job paused at the approval step
- [ ] You opened the staged output review view in the Actions UI
- [ ] You approved the run and confirmed the write executed correctly after approval
- [ ] You can explain in one sentence the difference between a normal run and a staged run
- [ ] You can name at least one scenario where staged mode is preferable to an unattended run

<!-- journey: all -->
Want to explore more advanced topics? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

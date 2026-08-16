<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Run Agentic Workflows Across Multiple Repositories

> _Unlock organisation-wide automation by letting a single agentic workflow read data from — and write safely to — more than one repository._

## 🎯 What You'll Do

You'll update a workflow to target a second repository using the `cross-repository` feature of gh-aw. By the end of this step your workflow will read open issues from a source repository and post a summarised report as an issue comment in a separate target repository — a pattern common in enterprise portfolio dashboards.

## 📋 Before You Start

- You have a working daily-status or PR-reviewer workflow from earlier in the workshop (see [Test and Improve Your Workflow](12-test-and-iterate.md) or [Build Your First Event-Driven Workflow: PR Auto-Reviewer](14b-pr-reviewer-workflow.md)).
- You have write access to at least two repositories (or two separate forks). For enterprise learners: confirm both repositories exist in the same organization or that your token scope allows cross-org access.
- You have already set up [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) — it provides the audit trail you will want when workflows touch multiple repos.

> [!NOTE]
> Enterprise Server users should confirm that both repositories are hosted on the same GHES instance. Cross-instance reads require additional network configuration outside the scope of this step.

## Steps

### Create a target repository

You need a second repository to receive the report. If you already have one, skip ahead.

1. Go to [github.com/new](https://github.com/new).
2. Name it `aw-portfolio-report`, set it to **Private**, and tick **Add a README file**.
3. Click **Create repository**.

### Add the cross-repository declaration to your workflow

Open your existing workflow file (e.g., `.github/workflows/daily-report-status.md`) in the GitHub UI or your editor.

In the frontmatter, add a `repositories` block that names both repos:

```yaml
---
name: Cross-Repository Status Report
on:
  schedule: daily on weekdays
  workflow_dispatch:

permissions:
  issues: write

repositories:
  - owner: YOUR_USERNAME
    repo: YOUR_SOURCE_REPO
    access: read
  - owner: YOUR_USERNAME
    repo: aw-portfolio-report
    access: write

safe-outputs:
  create-issue-comment:
    limit: 1
---
```

Replace `YOUR_USERNAME` and `YOUR_SOURCE_REPO` with your actual values.

> [!TIP]
> The `access: read` entry is optional — gh-aw defaults to read for any listed repository. Include it explicitly so the intent is clear to reviewers.

### Update the agent brief

Below the frontmatter, update the Markdown body (the agent instructions):

```markdown
You are a portfolio reporter.

1. Read the 10 most recently updated open issues from `YOUR_USERNAME/YOUR_SOURCE_REPO`.
2. Group them by label. Issues with no label go under "Unlabelled".
3. Write a 3–5 sentence summary covering: total open issue count, the most active label group,
   and any issue open more than 14 days without a comment.
4. Post the summary as a new issue comment on issue #1 of `YOUR_USERNAME/aw-portfolio-report`
   using the `create-issue-comment` safe output.
```

### Compile and validate

If you are on a terminal or in a Codespace, recompile the workflow:

```bash
gh aw compile
```

Commit both the `.md` and the updated `.lock.yml`.

**UI-first learners:** the browser editor triggers compilation on the next push. Watch the run log for errors and fix them before re-triggering.

### Trigger a manual run

1. Go to the **Actions** tab of your source repository.
2. Find your workflow and click **Run workflow**.
3. Watch the run log. You should see the agent read issues from the source repo and then post to `aw-portfolio-report`.

![Screenshot showing a cross-repository workflow run log with two repository operations](images/27-cross-repo-run-log.png)

### Confirm the output

1. Navigate to `YOUR_USERNAME/aw-portfolio-report`.
2. Open **Issues → Issue #1**.
3. Scroll to the bottom — your workflow should have posted a comment with the portfolio summary.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `repositories` block with at least two entries
- [ ] One repository has `access: read` (or default read) and the other has `access: write`
- [ ] `gh aw compile` completed without errors (or the Actions run compiled successfully)
- [ ] Both `.md` and `.lock.yml` are committed and pushed
- [ ] The workflow ran and posted a comment on an issue in the target repository
- [ ] You can explain why explicitly listing repositories in frontmatter is a security best practice

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

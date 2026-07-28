<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _As more teams adopt agentic workflows, a lightweight governance model prevents runaway costs, scope creep, and security surprises._

## 🎯 What You'll Do

In this step, you will apply a practical governance checklist to the workflow you have built. You will review naming conventions, document the intended scope of your workflow, add a `protected-files` guard, and link your workflow to an issue template so that changes go through a review process. By the end, you will have a workflow that is safe to hand off to other teams.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your `daily-status.md` workflow is compiling and running without errors.
- You understand the `permissions:` and `safe-outputs:` fields from earlier steps.

## Steps

### Understand why governance matters

An agentic workflow can read repository data, call AI models, post issues, and open pull requests — all unattended. In a personal repository that is fine. Across a team or organisation it becomes a shared resource that can:

- Consume AI Credits faster than expected if a brief sends the model in circles.
- Write to files or branches that other workflows depend on.
- Expand its own scope if someone edits the brief without review.

Governance is how you keep that power bounded and visible.

### Apply the naming convention

Give your workflow file and its frontmatter `name:` field a name that reveals its trigger, author team, and action at a glance. The convention used across this workshop is:

```
<trigger>-<team>-<action>.md
```

For example: `schedule-platform-daily-status.md`.

If your file is still named `daily-status.md`, that is fine for a personal repository. For a team workflow, rename it:

<details>
<summary>🖥️ GitHub UI rename</summary>

1. Open the file in your repository on GitHub.
2. Click the **pencil icon (✏️)** to edit.
3. Click on the filename at the top of the editor and change it.
4. Scroll to **Commit changes** and commit with a short message like `chore: apply naming convention`.

</details>

```bash
git mv .github/workflows/daily-status.md .github/workflows/schedule-platform-daily-status.md
git commit -m "chore: apply naming convention"
git push
gh aw compile
```

### Add `protected-files`

The `protected-files:` frontmatter field lists paths the agent must never modify, even if its brief says to. Add it to protect your compiled lock file and any sensitive configuration:

```yaml
---
name: Schedule · Platform · Daily Status
on:
  schedule:
    - cron: "0 9 * * 1-5"
  workflow_dispatch:

permissions:
  contents: read
  issues: write
  pull-requests: write

protected-files:
  - "*.lock.yml"
  - ".github/workflows/**"
  - "CODEOWNERS"
---
```

Compile after editing to confirm the frontmatter is valid:

```bash
gh aw compile
```

### Document intent with a workflow header comment

Add a brief comment block at the top of the Markdown body — after the closing `---` fence and before the agent brief. This comment is ignored by the runner but is invaluable for reviewers:

```markdown
<!--
  Owner: @platform-team
  Purpose: Post a daily summary of open issues and recent commits to the #status issue.
  Escalation: Open an issue tagged `workflow-review` for any brief changes.
  Last reviewed: <!-- date -->
-->
```

> [!TIP]
> The `<!-- date -->` placeholder is a deliberate reminder. Replace it with today's date when you set up a review cadence.

### Create a `WORKFLOW_CHANGE` issue template

Changes to agentic workflow briefs are code changes — they can alter what the agent does and how many AI Credits it consumes. A simple issue template makes that explicit.

In your repository, create `.github/ISSUE_TEMPLATE/workflow-change.yml`:

<details>
<summary>🖥️ GitHub UI path</summary>

1. Navigate to your repository and click **Add file → Create new file**.
2. Type `.github/ISSUE_TEMPLATE/workflow-change.yml` in the filename box.
3. Paste the content below.
4. Click **Commit new file**.

</details>

```yaml
name: Workflow Change Request
description: Propose a change to an agentic workflow brief or frontmatter
labels: [workflow-review]
body:
  - type: input
    id: workflow
    attributes:
      label: Workflow file
      placeholder: ".github/workflows/schedule-platform-daily-status.md"
    validations:
      required: true
  - type: textarea
    id: change
    attributes:
      label: Proposed change
      description: What will you change and why?
    validations:
      required: true
  - type: textarea
    id: impact
    attributes:
      label: Expected impact
      description: "Will this affect: AI Credit consumption, permissions, safe-outputs, schedule frequency?"
    validations:
      required: true
```

### Require a review before merging workflow changes (optional)

If your repository has branch protection enabled, add `.github/workflows/` to the list of paths that require a code review. This means every edit to a workflow file must be approved before it merges.

In your repository **Settings → Branches → Branch protection rules**, edit the rule for `main` and add a required review for changes to `.github/workflows/**`.

> [!NOTE]
> Branch protection is a repository-level setting, not a workflow setting. It applies regardless of which team member commits.

## ✅ Checkpoint

- [ ] Your workflow file follows the `<trigger>-<team>-<action>.md` naming convention (or you have a documented reason for keeping the current name)
- [ ] You added a `protected-files:` block guarding at least `*.lock.yml` and `.github/workflows/**`
- [ ] You added a header comment documenting the owner, purpose, and escalation path
- [ ] You created (or reviewed the template for) a `WORKFLOW_CHANGE` issue template in `.github/ISSUE_TEMPLATE/`
- [ ] You can describe in one sentence what governance problem each of these four additions solves

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

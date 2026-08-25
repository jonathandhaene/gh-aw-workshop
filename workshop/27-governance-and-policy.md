<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _AI-powered automation is only as trustworthy as the guardrails around it — learn to enforce consistent policy before your workflows reach production._

## 🎯 What You'll Do

Set up organisation-level controls that govern which agentic workflows can run, who must approve them, and what they are allowed to do. By the end of this step you will have a required-reviewer rule for workflow changes and a clear mental model for scaling agentic workflows safely across a team or enterprise.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one agentic workflow running in a GitHub repository.
- You have admin access to the repository (or a test organisation you can experiment in).

> [!NOTE]
> If you are on GitHub Enterprise Server (GHES) or GitHub Enterprise Cloud (GHEC), your organisation administrator may have already enforced some of these controls. Check with them before applying changes.

## Steps

### Understand the three governance layers

Agentic workflow governance operates at three levels. Each layer adds trust without slowing down everyday development.

| Layer | Where it lives | What it controls |
|---|---|---|
| **Repository policy** | Branch protection rules | Which commits to workflow files require review |
| **Workflow permissions** | `permissions:` frontmatter block | What each workflow is allowed to read or write |
| **Organisation policy** | Enterprise or organisation settings | Whether agentic workflows can run at all, and under what conditions |

Start at the repository layer and work outward as your team grows.

### Restrict changes to workflow files with a branch protection rule

Workflow files in `.github/workflows/` carry significant authority. Limit who can merge changes to them without a review.

1. In your repository, click **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit the existing rule for your default branch).
3. Under **Require a pull request before merging**, enable **Required approvals** and set the count to **1** (or higher for production repositories).
4. Under **Restrict file paths**, add `.github/workflows/**` to require an additional review any time a workflow file changes.
5. Click **Save changes**.

> [!TIP]
> For enterprise teams, consider creating a dedicated **workflow-approvers** team and adding it as a required reviewer. This separates AI workflow governance from general code review.

### Lock down `permissions:` in every workflow

The `permissions:` block in your workflow frontmatter is your first defence against over-privileged AI agents. Follow the principle of least privilege.

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  issues: write
  contents: read
---
```

Check every workflow you own:

- Remove `write` permissions that the workflow does not use.
- Never use `permissions: write-all` in agentic workflows — it gives the AI agent unrestricted access to your repository.
- Prefer `contents: read` unless your workflow must commit or create releases.

<details>
<summary>🖥️ Review permissions in the GitHub UI</summary>

1. Navigate to your workflow file in **Code** view.
2. Click the **pencil icon (✏️)** to open the editor.
3. Scroll to the top of the YAML frontmatter.
4. Review the `permissions:` block. Adjust and **Commit changes** if needed.

</details>

### Define a workflow approval policy (enterprise)

On GitHub Enterprise Cloud, you can require manual approval before any Actions workflow run that was triggered by an external event or a newly forked repository.

1. Go to **Settings** → **Actions** → **General** at the organisation level.
2. Under **Fork pull request workflows**, choose **Require approval for all outside collaborators**.
3. Under **Workflow permissions**, select **Read repository contents and packages permissions** as the default.
4. Click **Save**.

This ensures no agentic workflow runs silently on code from an untrusted source.

> [!NOTE]
> On GitHub Enterprise Server, these settings are found in the admin console under **Policies** → **Actions**. Exact labels vary by GHES version.

### Document your governance baseline

Create a short `WORKFLOW_POLICY.md` in your `.github` repository (or at the repo root) listing which workflows are approved, what permissions are allowed, who reviews changes, and how to request new capabilities. This becomes your audit anchor when onboarding new contributors.

## ✅ Checkpoint

- [ ] Your default branch has a protection rule that requires at least one review for changes to `.github/workflows/**`
- [ ] Every agentic workflow you own has an explicit `permissions:` block with the minimum required access
- [ ] None of your workflows use `permissions: write-all`
- [ ] You can describe the difference between repository-level and organisation-level policy
- [ ] You have created or identified a location for your team's workflow policy document

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

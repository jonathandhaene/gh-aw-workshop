<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enforce Governance Policies Across Your Organization

> _Agentic workflows are powerful — governance ensures that power is applied consistently, safely, and within your organization's risk appetite._

## 🎯 What You'll Do

You'll learn how to apply org-level controls to agentic workflows: restricting which repositories can run them, setting default permissions, and using required reviewers and environment protection rules. By the end, you'll have a clear policy framework you can adapt for your team or organization.

## 📋 Before You Start

- You've completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to a GitHub organization (or a personal account acting as one for practice).
- You understand how GitHub Actions environments and protection rules work at a basic level.

## Steps

### Understand the governance model

Agentic workflows run as GitHub Actions jobs. Every governance control that applies to Actions applies here too — plus a few extras that `gh-aw` adds.

There are three layers of control:

1. **Organization-level**: policies that apply to every repository in your org.
2. **Repository-level**: settings that scope what a single repo's workflows can do.
3. **Workflow-level**: frontmatter options (`permissions:`, `max-ai-credits:`) that the author sets explicitly.

Think of these as concentric rings. Org policies set the outer boundary; workflow authors work within it.

### Set Actions policies at the org level

Navigate to your organization settings:

1. Go to **github.com/organizations/YOUR-ORG/settings/actions** (replace `YOUR-ORG` with your org name).
2. Under **Policies**, choose which repositories can run Actions:
   - **All repositories** — any repo in your org.
   - **Selected repositories** — you manually allow specific repos.
3. Under **Workflow permissions**, set the default `GITHUB_TOKEN` permission to **Read repository contents and packages** (least-privilege default).
4. Decide whether to allow **Actions to create and approve pull requests** — for agentic workflows that write back to repos, this must be enabled.

> [!NOTE]
> Agentic workflows that use `safe-outputs` tools (like creating PRs or posting comments) require the `pull-requests: write` and `issues: write` permissions. Set workflow-level `permissions:` in your frontmatter to grant only what each workflow needs.

### Use environments for high-stakes workflows

GitHub Actions [environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) add an approval gate before a workflow can run. Use this for workflows that write to production branches, post to external systems, or consume significant AIC.

1. In your repository, go to **Settings → Environments → New environment**.
2. Name it (e.g., `production-agentic`).
3. Add **Required reviewers** — one or more people who must approve each run.
4. In your workflow frontmatter, reference the environment:

```yaml
---
name: Governance-Gated Workflow
on:
  schedule: weekly on Monday at 09:00
permissions:
  contents: read
  issues: write
environment: production-agentic
max-ai-credits: 500
---
```

Commit and compile the workflow:

```bash
gh aw compile
```

Now every run of this workflow requires a manual approval before the agent executes.

<details>
<summary>🖥️ GitHub UI alternative for committing the frontmatter change</summary>

1. Navigate to your workflow `.md` file in the repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Update the frontmatter to add `environment: production-agentic`.
4. Click **Commit changes** and push to your default branch.

Note: you still need to run `gh aw compile` locally (or trigger a CI step that compiles) to regenerate the `.lock.yml` before Actions will pick up the change.

</details>

### Audit who can create and modify workflows

Anyone with write access to a repository can add a `*.lock.yml` file and trigger an agentic workflow. Tighten this with branch protection:

1. Go to **Settings → Branches → Add rule** for your default branch.
2. Enable **Require pull request reviews before merging**.
3. Enable **Require status checks to pass** — add your compile CI check as a required status.

This ensures every new or modified workflow goes through a review before it runs in the default branch.

> [!TIP]
> The [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/) covers additional enterprise patterns including GHES site-admin controls and GHEC enterprise policies. Bookmark it as a reference for your policy rollout.

### Document your policy

Create a `WORKFLOW_POLICY.md` in your repository's `.github/` folder. Include:

- Which workflow categories are approved (e.g., reporting, labeling, analysis).
- The default `max-ai-credits` cap for new workflows.
- Which environments require approval gates.
- The review process for new workflow proposals.

Even a short policy document reduces friction when teammates want to add their own workflows.

## ✅ Checkpoint

- [ ] You set a default `GITHUB_TOKEN` permission (read-only) at the organization level
- [ ] You created an environment with at least one required reviewer
- [ ] Your workflow frontmatter includes `permissions:` and `environment:` fields
- [ ] `gh aw compile` passes without errors on the updated workflow file
- [ ] Branch protection rules require a PR review before merging workflow file changes
- [ ] You created or outlined a `WORKFLOW_POLICY.md` document for your team

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

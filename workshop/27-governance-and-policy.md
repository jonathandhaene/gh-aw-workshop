<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _A single workflow running in one repo is easy to trust — but when dozens of teams start shipping agentic workflows, governance becomes the difference between reliable automation and unpredictable risk._

## 🎯 What You'll Do

You'll learn how to apply org-level policy controls to agentic workflows: limiting which workflows can run, controlling the permissions they receive, and establishing a review gate so new agentic workflows are approved before they run in production.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are an organisation owner, or your GitHub admin can confirm the settings you see in the steps below.
- _(Enterprise users)_ Review the [Enterprise Setup Considerations](side-quest-enterprise-setup.md) side quest if you haven't already.

## Steps

### Understand the permission model for agentic workflows

Every agentic workflow run receives a short-lived `GITHUB_TOKEN`. That token's permissions are scoped by two layers:

1. **Repository default permissions** — the baseline read or read/write access your org grants all Actions runs.
2. **Workflow-level permissions** — the `permissions:` block in the workflow frontmatter, which can only narrow the token, never expand beyond the org default.

This layered model means the safest posture is to set the org default to **read-only**, then explicitly grant the specific `write` permissions each workflow actually needs.

To set the org default:

1. Go to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Workflow permissions**, select **Read repository contents and packages permissions**.
4. Click **Save**.

> [!NOTE]
> This change applies to all new workflow runs. Existing runs already in progress are not affected.

### Review per-workflow permission declarations

After tightening the org default, each agentic workflow that writes back to GitHub needs an explicit `permissions:` block in its frontmatter.

Open your daily-status workflow file and confirm it declares only what it needs:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
---
```

If your workflow does not write issues, remove `issues: write`. Only grant permissions the workflow actively uses.

> [!TIP]
> Run `gh aw audit <run-id>` to see exactly which GitHub API calls a past run made. Use that list to verify your declared permissions are neither too broad nor missing any required scope.

### Require approval for workflows from fork pull requests

If your repository accepts pull requests from forks, enforce manual approval before agentic workflow runs can access secrets or your token.

1. Go to **Settings** → **Actions** → **General** in your repository.
2. Under **Fork pull request workflows**, select **Require approval for all outside collaborators**.
3. Click **Save**.

> [!NOTE]
> On GitHub Enterprise Server, this setting may be managed at the org level by an admin. Check with your administrator if you don't see the option in repository settings.

### Add a required workflow review gate (org-level)

For teams shipping agentic workflows to a shared organisation, require that a security or governance team reviews and approves new workflow files before they merge to the default branch.

Set this up with a CODEOWNERS file:

1. Create or edit `.github/CODEOWNERS` in each repository that hosts agentic workflows.
2. Add an entry that maps your workflow files to an approving team:

```
.github/workflows/*.md @your-org/workflow-reviewers
.github/workflows/*.lock.yml @your-org/workflow-reviewers
```

1. Commit and push the file.
2. In your repository, go to **Settings** → **Branches** → **Add branch protection rule** for your default branch.
3. Enable **Require a pull request before merging** and **Require review from Code Owners**.

Now any change to a workflow file requires sign-off from your designated reviewers.

<details>
<summary>🖥️ GitHub UI alternative for adding CODEOWNERS</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Name it `.github/CODEOWNERS`.
4. Paste the ownership rules above, replacing `@your-org/workflow-reviewers` with your real team slug.
5. Click **Commit new file**.

</details>

### Document your governance decisions

Agentic workflows are code. Treat them like code:

- Record which workflows are approved to run in which repositories.
- Capture the intended permissions and data sources for each workflow.
- Link the audit log from [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) to your runbook or incident response playbook.

A lightweight record — even a GitHub Issue template that teams fill in before shipping a new workflow — dramatically reduces the cost of reviewing an incident later.

## ✅ Checkpoint

- [ ] Your organisation's default workflow permissions are set to **read-only**
- [ ] Your daily-status workflow frontmatter declares only the permissions it actually uses
- [ ] You have enabled approval for fork pull request workflows (or confirmed your org policy covers this)
- [ ] Your repository has a `CODEOWNERS` entry routing workflow file changes to a review team
- [ ] A branch protection rule requires code owner approval before workflow changes merge
- [ ] You can describe the two-layer permission model (org default + workflow-level declaration) in your own words

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

# Govern Your Agentic Workflows

> _Governance turns a powerful tool into a trustworthy one — learn how to apply policies, track approvals, and meet compliance requirements before your workflows reach production._

## 🎯 What You'll Do

You will add a lightweight governance layer to your agentic workflows: required reviewers, environment protection rules, and a policy document your team can reference. By the end of this step, your workflow will be blocked from running in production without an explicit human approval.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow is deployed to a repository where you have **Admin** access (or a collaborator with that role can help).
- If your organisation runs GitHub Enterprise Cloud or GitHub Enterprise Server, ask your GitHub admin whether required reviewers or deployment policies are already enforced at the org level.

## What "governance" means here

Agentic workflows write things — comments, labels, issues, pull requests. That power calls for guardrails. Governance in this context means:

- **Who can approve a workflow run** before it touches production data.
- **Which environments** a workflow is allowed to deploy to or write in.
- **An audit trail** that records who approved each run and when.

GitHub's built-in [Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) feature provides all three without any extra tooling.

## Create a protected environment

### Open repository settings

1. Go to your repository on GitHub.
2. Click **Settings** → **Environments** (in the left sidebar under "Code and automation").
3. Click **New environment**.
4. Name it `production` and click **Configure environment**.

![Environments settings page showing the New environment button](images/27-environments-new.png)

### Add required reviewers

1. Under **Required reviewers**, click **Add required reviewers**.
2. Search for your GitHub username and select it.
3. Click **Save protection rules**.

From now on, any workflow run that targets the `production` environment will pause and wait for your approval before executing the agentic steps.

> [!TIP]
> In a team setting, add a team (e.g. `@your-org/platform-engineers`) as a required reviewer rather than an individual. That way the approval gate survives personnel changes.

## Update your workflow to target the environment

Open your agentic workflow file (for example `.github/workflows/daily-report.md`) and add an `environment` key to the frontmatter:

```yaml
---
name: Daily Repository Status Report
on:
  schedule:
    - cron: "0 9 * * 1-5"
environment: production
permissions:
  issues: write
  contents: read
---
```

Commit the change. The next scheduled run will pause at the environment gate and notify you by email.

<details>
<summary>🖥️ GitHub UI alternative — edit the workflow file</summary>

1. In your repository on GitHub, navigate to `.github/workflows/your-workflow.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `environment: production` line under your existing frontmatter keys.
4. Click **Commit changes**, write a short commit message, and commit to the default branch.

</details>

## Write a governance policy document

A policy document gives your team a single source of truth. Create `.github/AGENTIC_POLICY.md` in your repository:

```markdown
# Agentic Workflow Policy

## Scope
All workflows that use the `copilot` or `github-models` engine and write to issues,
pull requests, or repository contents.

## Approval requirement
Production runs require approval from at least one member of the `platform-engineers` team.

## Permitted tools
Agentic steps may use only the tools listed in the workflow frontmatter `tools:` block.
MCP server additions require a separate pull-request review.

## Secret handling
Agentic workflows must not log secret values. Use `safe-outputs` for all write operations.

## Retention
Audit logs are retained for 90 days in accordance with our data-retention policy.
```

Commit this file to your repository so it appears on the repository's main page and in pull request templates.

## Enterprise considerations

| Topic | What to do |
|-------|-----------|
| **GHES policy enforcement** | Ask your admin to enable "Required reviewers for deployments" at the organisation level — this overrides any repository-level setting that is weaker. |
| **GHEC enterprise policy** | Under your enterprise account → **Policies** → **Actions**, confirm that environment protection rules are allowed for all repositories. |
| **Compliance exports** | Use `gh api /repos/{owner}/{repo}/deployments` to export a JSON audit trail of all environment deployments for compliance reporting. |
| **SAML/SSO** | If your organisation enforces SAML SSO, ensure the token used by the agentic workflow is an authorised personal access token or GitHub App installation token — `GITHUB_TOKEN` works automatically. |

## ✅ Checkpoint

- [ ] You created a `production` environment in your repository settings
- [ ] You added at least one required reviewer to the `production` environment
- [ ] You added `environment: production` to your agentic workflow's frontmatter and committed the change
- [ ] You verified that the next manual or scheduled workflow run pauses for approval before executing
- [ ] You created `.github/AGENTIC_POLICY.md` with a scope, approval, and tool-use policy
- [ ] You reviewed the enterprise considerations table and noted any actions relevant to your environment

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

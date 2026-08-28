<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _As agentic workflows multiply across teams, governance turns individual good practices into organisation-wide guarantees — so every workflow runs safely, consistently, and within policy._

## 🎯 What You'll Do

You'll learn how to apply organisation-level controls to agentic workflows: required review policies, permitted model and tool allowlists, and shared reusable workflow templates that enforce standards without slowing teams down.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have organisation owner or administrator access, or your GitHub administrator is available.
- _(GHES users)_ Confirm with your site admin that GitHub Actions is enabled and Copilot Enterprise is licensed at the organisation level.

## Steps

### Understand the governance layers

Agentic workflow governance operates at three levels: **repository** (workflow author controls `permissions:`, `safe-outputs:`, and `network.allowed-domains`), **organisation** (owner controls allowed actions and Copilot policy), and **enterprise** (site admin controls Copilot Enterprise enablement and audit log retention). For most teams, organisation-level settings are where daily governance decisions live.

### Review your organisation's Actions policy

1. Go to your organisation on GitHub and click **Settings**.
2. In the left sidebar, click **Actions → General**.
3. Under **Actions permissions**, confirm that only trusted sources are allowed to run.
4. Under **Fork pull request workflows**, verify that workflows triggered by forks require approval.

> [!NOTE]
> Agentic workflows run inside GitHub Actions. Any Actions policy that restricts workflow execution also applies here. See the [GitHub Actions permissions docs](https://docs.github.com/en/organizations/managing-organization-settings/disabling-or-limiting-github-actions-for-your-organization) for the full list of controls.

### Set a Copilot usage policy for agentic workflows

Copilot usage in Actions is controlled separately from IDE usage.

1. In your organisation settings, click **Copilot → Policies**.
2. Confirm that **Copilot in GitHub Actions** is set to the appropriate scope — **Enabled for all repositories**, **Selected repositories**, or **Disabled**.
3. If you are on GitHub Enterprise Cloud (GHEC), check whether a [Copilot Enterprise policy](https://docs.github.com/en/enterprise-cloud@latest/admin/policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-github-copilot) applies at the enterprise level and overrides the organisation setting.

> [!TIP]
> Restricting Copilot in Actions to **Selected repositories** is a good starting point for production orgs. You can expand the list as teams complete a workflow review.

### Create a shared reusable workflow template

Reusable workflows let you codify standard frontmatter — approved models, permission ceilings, and required audit steps — in one place. Teams call the template instead of writing their own.

1. In a `.github` repository in your organisation, create `.github/workflow-templates/agentic-standard.md`.
2. Add the shared defaults your organisation requires:

   ```yaml
   ---
   name: Standard Agentic Workflow
   on:
     workflow_call: {}
   permissions:
     contents: read
   engine: copilot
   network:
     allowed-domains:
       - api.github.com
   ---
   ```

3. Commit and push. Teams override only what they need; the shared baseline prevents accidental over-scoping.

> [!NOTE]
> For the full syntax — including `inputs:` and `secrets:` — see the [Reusing Agentic Workflows guide](https://github.github.com/gh-aw/guides/reusing-workflows/).

### Require pull request review for workflow changes

Requiring a review before a workflow file merges prevents accidental or malicious changes from going live.

1. In your repository settings, click **Branches → Add branch protection rule**.
2. Set the branch to `main` (or your default branch).
3. Enable **Require a pull request before merging** and set **Required approvals** to at least **1**.
4. Enable **Require review from Code Owners** if your organisation's `CODEOWNERS` file maps `.github/workflows/` to a trusted team.

### Audit org-wide workflow usage

Use `gh aw audit` at the organisation level to identify which workflows are running, how often, and at what cost.

```bash
gh aw audit --org YOUR_ORG_NAME --output audit-report.md
```

Review the report for over-scoped `permissions:`, unexpected `network.allowed-domains` entries, missing `safe-outputs:` declarations, and unusually high AIC consumption. Share the report with your GitHub administrator at each quarterly review cycle.

## ✅ Checkpoint

- [ ] You reviewed your organisation's Actions permissions and confirmed the fork policy
- [ ] You located the Copilot in Actions policy in your organisation or enterprise settings
- [ ] You can describe the three governance layers and who controls each one
- [ ] You created or reviewed a shared reusable workflow template with safe baseline frontmatter
- [ ] You enabled pull request reviews for changes to `.github/workflows/` in at least one repository
- [ ] You ran `gh aw audit --org` and identified at least one finding to follow up on

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

<!-- page-journey: all -->
<!-- page-adventure: enterprise -->
# Govern Agentic Workflows Across Your Organization

> _One workflow is a tool — dozens of workflows across many teams are a policy challenge. Learn how to keep them safe, consistent, and cost-controlled at scale._

## 🎯 What You'll Do

You'll review the governance controls available in GitHub for managing agentic workflows across an organization: required approval policies, org-wide permission defaults, a shared workflow catalog, and an audit strategy. By the end of this step you'll have a governance checklist you can bring to your team.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md), or you are an org admin who wants the policy picture before going deep on individual features.
- You have owner or admin access to a GitHub organization (or you are evaluating what access you'll need).

## Steps

### Understand what "governance" means for agentic workflows

An agentic workflow can read your repository, call an AI model, and write back to GitHub on a schedule — all without human approval. Governance means answering four questions:

1. **Who can create and run workflows?** (access control)
2. **What can a workflow do?** (permission and safe-output scope)
3. **What did a workflow actually do?** (audit trail)
4. **How much is it costing?** (budget controls)

Questions 3 and 4 are covered in [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md). This step focuses on questions 1 and 2 — and on operationalizing all four across teams.

### Set organization-level Actions policies

In your organization settings, navigate to **Settings → Actions → General** to configure the baseline:

- **Allow all actions** vs **Allow select actions** — limit which actions can be called from workflow files. For agentic workflows, the key entry point is the `gh-aw` action; block unknown third-party actions by default.
- **Workflow permissions** — set the organization default to `Read repository contents and packages permissions`. Individual workflows must explicitly declare wider permissions in their frontmatter `permissions:` block.
- **Fork pull request workflows** — configure whether workflows from forked PRs require approval before running. For repositories that accept external contributions, set this to **Require approval for first-time contributors**.

> [!NOTE]
> These settings apply to all GitHub Actions workflows in the organization, not just agentic ones. Tightening defaults here protects you against any overly permissive workflow.

### Define a minimum `permissions:` standard for your org

Every agentic workflow frontmatter should start with the smallest permissions needed. Publish a team standard in your organization's internal wiki or `CONTRIBUTING.md`. For example:

```yaml
permissions:
  contents: read
  issues: write
```

Workflows that need more than `issues: write` should go through a lightweight review before merging. Enforce this with a `CODEOWNERS` rule on `.github/workflows/`:

```
.github/workflows/   @your-org/workflow-reviewers
```

Any pull request that adds or changes a workflow file will automatically request a review from the `workflow-reviewers` team.

### Build an org-wide workflow catalog

Use a dedicated repository — for example `your-org/agentic-workflow-templates` — as a central catalog of approved, reviewed workflow files. Teams install from it with:

```bash
gh aw add your-org/agentic-workflow-templates/<workflow-name>
```

Benefits:
- `permissions:` and `safe-outputs:` blocks act as a reviewed security baseline.
- Teams get a working starting point instead of writing from scratch.
- You can update templates and ask teams to re-import when the standard changes.

To add a workflow to the catalog, the template author opens a pull request to `agentic-workflow-templates`. Your `workflow-reviewers` team reviews the `permissions:`, `safe-outputs:`, and task brief before merging.

### Review required workflow approvals for sensitive repositories

For repositories that hold production configuration or sensitive data, consider requiring a manual approval before scheduled agentic workflows run. In **Settings → Actions → General → Required approvals**, set the required reviewers to one or more trusted team members.

This adds a human checkpoint before the workflow can call the AI model and write back — a useful control in regulated environments.

### Review the gh-aw governance guide

The gh-aw documentation includes a governance guide at [`https://github.github.com/gh-aw/guides/governance/`](https://github.github.com/gh-aw/guides/governance/). Read the **Policy controls** and **Org-wide defaults** sections before rolling out to multiple teams.

## ✅ Checkpoint

- [ ] You can describe the four governance questions and which gh-aw controls answer each one
- [ ] You have reviewed your organization's Actions → General settings and know which values to change
- [ ] You have drafted a minimum `permissions:` standard for your team
- [ ] You know how to set up a `CODEOWNERS` rule to require review for workflow file changes
- [ ] You have a plan (or an existing repository) for a shared workflow catalog
- [ ] You know how to enable required approvals for sensitive repositories

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

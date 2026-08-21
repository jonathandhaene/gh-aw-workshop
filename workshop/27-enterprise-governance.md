<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enforce Organisation-Wide Governance for Agentic Workflows

> _Governance turns individual cost and security controls into consistent, auditable policy that your whole organisation can rely on._

## 🎯 What You'll Do

You'll apply organisation-level controls that restrict which workflows can run, which AI models they can call, and which secrets they can access. By the end of this step you'll have a governance checklist your team can use before promoting any new agentic workflow to production.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have org **Owner** or **Security Manager** access, or you can ask a GitHub admin to apply the settings.
- You know whether your environment is `github.com`, GitHub Enterprise Cloud (GHEC), or GitHub Enterprise Server (GHES). Some controls below are GHEC/GHES-only; they're marked accordingly.

## Steps

### Decide which AI models your org allows

Agentic workflows declare the model they want in their frontmatter:

```yaml
---
model: copilot/gpt-4o
---
```

You can restrict which models are available in your organisation:

1. Go to your organisation's page on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Under **Model access**, select the models you want to permit. Disable any that are not approved by your security or procurement team.

> [!NOTE]
> On GHES, model availability also depends on the version of GitHub Enterprise Server and the external model endpoint your administrator has configured. Check with your admin before setting policy.

### Apply repository-level Actions permissions

Governance starts with restricting what can run at all. In each repository that hosts agentic workflows:

1. Navigate to the repository on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Actions permissions**, choose **Allow select actions and reusable workflows**.
4. Add the `github/gh-aw` action to the allow-list. This prevents unapproved actions from running alongside your agentic workflows.

> [!TIP]
> You can enforce the same Actions permissions policy at org level under **Organisation settings → Actions → General**. Org-level policy takes precedence over repository-level settings when it is more restrictive.

### Protect secrets with environment protection rules

Agentic workflows that call external APIs or write to sensitive systems should consume secrets from a **GitHub Environment** with required reviewers — not from the repository or organisation secret store directly.

1. In the repository, go to **Settings** → **Environments** → **New environment**.
2. Name the environment (e.g., `production-aw`).
3. Enable **Required reviewers** and add at least one person or team.
4. Move sensitive secrets (API keys, write tokens) into this environment.
5. Reference the environment in your workflow frontmatter:

```yaml
---
environment: production-aw
---
```

Now the workflow can only run in that environment after a human approves it — adding a mandatory checkpoint before any write action runs unattended.

### Audit permissions before promoting a workflow

Before merging a new agentic workflow into your default branch, review its `permissions:` block:

```yaml
---
permissions:
  contents: read
  issues: write
---
```

Apply the **principle of least privilege**: grant only the permissions the workflow demonstrably needs. If the `permissions:` block is absent, gh-aw defaults to the GITHUB_TOKEN's default scope — which may be broader than intended.

<details>
<summary>🖥️ Check permissions via the GitHub UI</summary>

1. Open the pull request that introduces or changes the workflow file.
2. In the **Files changed** tab, locate the workflow `.md` file.
3. Find the YAML frontmatter block (between the `---` fences at the top of the file).
4. Read the `permissions:` section. If it is missing, flag it for review before approving.

</details>

### Set org-wide spending limits

You set per-workflow cost guardrails in the previous step. For organisation-wide protection:

1. Go to **Organisation settings** → **Billing and plans** → **Spending limits**.
2. Set a monthly spending limit for Copilot that covers expected agentic workflow usage plus a safety margin.
3. Enable **usage alerts** at 50 % and 80 % of the limit so you get early warning before the cap is hit.

_(GHEC)_ You can also delegate budget management to individual teams. Under **Teams**, assign a team-level Copilot seat allocation so agentic workflow usage in one team's repositories cannot crowd out another team.

### Create a governance checklist issue template

Make governance repeatable by capturing it as an issue template in your repository.

1. In the repository, create `.github/ISSUE_TEMPLATE/aw-governance-review.md`.
2. Paste in the checklist below and commit it.

```markdown
---
name: Agentic Workflow Governance Review
about: Complete before promoting a new agentic workflow to production
---

## Workflow details

- **File:** 
- **PR:** 

## Governance checklist

- [ ] `permissions:` block is present and follows least privilege
- [ ] No secret references outside of a protected GitHub Environment
- [ ] AI model is on the approved model list
- [ ] `max-ai-credits` and `max-daily-ai-credits` are set
- [ ] `timeout-minutes` is set
- [ ] Actions permissions allow-list includes `github/gh-aw`
- [ ] At least one reviewer has approved the workflow brief for prompt-injection risk
- [ ] Audit and observability are enabled (see [Step 25](../workshop/25-audit-and-observability.md))
```

When a new agentic workflow is ready to ship, open one of these issues, complete the checklist, and close it as part of the merge process.

## ✅ Checkpoint

- [ ] You have reviewed your organisation's Copilot model access policy and confirmed which models are permitted
- [ ] You have applied or verified repository-level Actions permissions to restrict unapproved actions
- [ ] You have created at least one GitHub Environment with required reviewers for sensitive workflow secrets
- [ ] You have checked the `permissions:` block of your workflow and removed any permissions not strictly needed
- [ ] You have set an org-level Copilot spending limit with email alerts at 50 % and 80 %
- [ ] You have committed the governance checklist issue template to your repository

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

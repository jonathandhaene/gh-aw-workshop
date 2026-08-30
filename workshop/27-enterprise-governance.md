<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at Scale

> _Centrally controlling what agentic workflows can do — and proving they follow policy — is what makes them safe to deploy across an enterprise._

## 🎯 What You'll Do

You will learn how GitHub Enterprise Cloud (GHEC) and GitHub Enterprise Server (GHES) administrators enforce consistent policy for agentic workflows. You will configure required policy controls in your own repository and understand where enterprise admins can enforce those controls at the organization and enterprise level.

By the end, you will have a workflow that is ready for enterprise compliance review.

## 📋 Before You Start

- You have a working agentic workflow from the earlier steps (any path).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You know whether your environment is `github.com`, GHEC, or GHES. If you are on GHES, confirm version 3.12+ with your admin (see [Enterprise Setup Considerations](side-quest-enterprise-setup.md)).

## Steps

### Understand the policy layers

Agentic workflows run inside GitHub Actions, so the same four-layer policy stack applies:

| Layer | Who controls it | What it controls |
|---|---|---|
| **Enterprise policy** | Enterprise admin | Which organizations can run Actions and use Copilot |
| **Organization policy** | Org admin | Allowed Actions, required approvals, Copilot billing method |
| **Repository settings** | Repo admin | Secrets, environment protection rules, Actions permissions |
| **Workflow frontmatter** | Workflow author | Permissions, tools, safe-outputs, cost caps |

As a workflow author you control only the last layer. Enterprise and org admins control the rest. Understanding all four layers helps you write workflows that pass review without requiring admin intervention.

### Apply minimum-privilege frontmatter

Every enterprise-ready workflow should declare the narrowest possible permissions. Open your workflow file and confirm the `permissions:` block grants only what is strictly needed:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
safe-outputs:
  - add-comment
timeout-minutes: 10
max-ai-credits: 1000
max-daily-ai-credits: 2500
---
```

The key rules:
- Default `contents: write` is too broad for most reporting workflows — scope down to `read`.
- List only the `safe-outputs` your workflow actually uses. Each entry is an allowlisted write action the agent can perform.
- Never add `secrets: read` or `admin` permissions unless the workflow explicitly needs them.

<details>
<summary>🖥️ GitHub UI alternative — verify permissions in the Actions settings</summary>

1. Navigate to your repository on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Workflow permissions**, confirm the default is set to **Read repository contents and packages permissions** (not **Read and write**).
4. Click **Save** if you change the setting.

This sets the _default_ for any workflow that doesn't declare explicit permissions. Your frontmatter `permissions:` block overrides this default for your specific workflow.

</details>

### Enable required reviewers for sensitive environments

For workflows that write to production branches or post public-facing content, add an environment with required reviewers.

In the GitHub UI:

1. Go to **Settings** → **Environments** → **New environment**.
2. Name it `production` (or a name that matches your use case).
3. Under **Required reviewers**, add one or more people or teams.
4. Reference the environment in your workflow frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
environment: production
permissions:
  contents: read
  issues: write
safe-outputs:
  - add-comment
---
```

Now every run must be approved before it executes. This is especially useful on GHEC where org admins can enforce environment protection rules across repositories.

### Check your organization's Copilot billing method

Your workflow needs a Copilot model to run. How that is billed affects which method to use:

| Billing method | What it means | What to do |
|---|---|---|
| **Organization centralized billing** | Your org admin has enabled Copilot for Actions | No extra secret needed — workflow uses org-level access |
| **Personal `COPILOT_GITHUB_TOKEN`** | You bill Copilot to your personal account | Add `COPILOT_GITHUB_TOKEN` as a repository secret |

Ask your enterprise or org admin which method is in effect. If you are unsure, see [Configure GitHub Copilot for Agentic Workflows](side-quest-06-03-copilot-token.md).

> [!NOTE]
> On GHES, the Copilot billing method is configured by your enterprise admin. Personal-token fallback may not be available on all GHES versions.

### Request a compliance review (GHEC / GHES teams)

If your organization has a security or platform team, share your workflow file and the audit report from [Step 25](25-audit-and-observability.md) with them before promoting the workflow to production. Key things reviewers will check:

- `permissions:` follows least-privilege
- `safe-outputs:` is scoped to the minimum required write actions
- `network.allowed-domains:` (if present) lists only required external hosts
- `max-ai-credits` and `max-daily-ai-credits` are set
- The workflow does not read or write secrets beyond the Copilot token

## ✅ Checkpoint

- [ ] Your workflow frontmatter uses the narrowest `permissions:` block that still lets the workflow run
- [ ] You verified your repository's default workflow permissions are set to **Read** in **Settings → Actions → General**
- [ ] You understand the difference between organization centralized Copilot billing and personal-token billing
- [ ] You can describe the four policy layers (enterprise, org, repository, frontmatter) and who controls each
- [ ] (If applicable) You created an environment with required reviewers and referenced it in your frontmatter

<!-- journey: all -->
Want to return to the workshop hub? Go back to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

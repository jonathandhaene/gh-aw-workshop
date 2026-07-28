<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows in Your Organisation

> _Giving teams the freedom to build agentic workflows while keeping the organisation safe requires clear policies — learn where those controls live and how to apply them._

## 🎯 What You'll Do

You will review the organisation-level settings that control which AI models agentic workflows can use, how workflow runs are approved, and what permissions are available. By the end you will know how to set sensible guardrails so your team can ship confidently without unexpected access or spending surprises.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to the GitHub organisation you want to configure. If you are in a personal account, you can follow along in read-only mode.

> [!NOTE]
> Enterprise Cloud (GHEC) and Enterprise Server (GHES) users have additional policy layers managed by an enterprise admin. Where these differ from `github.com` defaults, notes are called out below.

## Steps

### Understand the three layers of governance

Governance for agentic workflows operates at three levels:

| Layer | Who controls it | What it covers |
|---|---|---|
| **Enterprise** | Enterprise admin | Allowed models, Copilot seat licensing, global `GITHUB_TOKEN` permission floor |
| **Organisation** | Org owner | Default workflow permissions, approval requirements, self-hosted runner groups |
| **Repository** | Repo admin | Individual workflow permissions, environment protection rules, secret access |

Each lower layer can only restrict, not expand, what the layer above grants.

### Set the default workflow permission level

Agentic workflow runs use the built-in `GITHUB_TOKEN` by default. Limiting that token reduces blast radius if a workflow behaves unexpectedly.

1. Go to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Workflow permissions**, select **Read repository contents and packages permissions** (the most restrictive default).
4. Click **Save**.

Individual workflows that genuinely need write access declare it explicitly in their frontmatter `permissions:` block — the org default does not block them, it just avoids granting write access where it was never requested.

> [!TIP]
> If you're on GHEC or GHES, the enterprise admin can enforce a minimum-permission floor that prevents org owners from choosing a more permissive default. Check **Enterprise settings** → **Policies** → **Actions** for that control.

### Require approval for first-time contributors

Prevent unknown contributors from triggering workflows that call AI models without a review step.

1. In **Settings** → **Actions** → **General**, find **Fork pull request workflows**.
2. Set it to **Require approval for first-time contributors**.
3. Click **Save**.

This means an org member must approve any workflow run triggered by a pull request from a first-time contributor, giving you a chance to review before any AI Credits are consumed.

### Control which AI models are available

You can limit which Copilot models your agentic workflows are allowed to use.

1. Go to **Settings** → **Copilot** → **Policies**.
2. Under **Models**, review the list of enabled models.
3. Disable models your organisation has not approved for use — for example, preview or third-party models.

Workflow frontmatter that requests a disabled model will fail at startup with a clear error rather than silently falling back, making policy violations obvious.

> [!NOTE]
> On GHES, model availability is also governed by network routing and the Copilot gateway configuration your admin has set up. Confirm available models with your admin before writing model-specific frontmatter.

### Use environment protection rules for sensitive workflows

Workflows that write to production systems or access secrets should target a **protected environment**.

1. Go to your repository's **Settings** → **Environments** → **New environment**.
2. Name it (e.g., `production`).
3. Enable **Required reviewers** and add at least one team or person.
4. Reference the environment in your workflow frontmatter:

```yaml
---
on:
  schedule:
    - cron: "0 9 * * 1-5"
jobs:
  report:
    environment: production
    runs-on: ubuntu-latest
---
```

Any run targeting `production` pauses and waits for a reviewer to approve before the agent executes.

## ✅ Checkpoint

- [ ] Your organisation's default workflow permission is set to read-only.
- [ ] First-time contributor approval is enabled.
- [ ] You have reviewed the list of allowed Copilot models and disabled any your org has not approved.
- [ ] At least one repository uses a protected environment for sensitive workflow runs.

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

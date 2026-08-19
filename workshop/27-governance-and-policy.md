<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Powerful automation needs guardrails — learn how to set organisation-wide policies that keep agentic workflows consistent, secure, and auditable._

## 🎯 What You'll Do

You'll explore the controls available to GitHub organisation and enterprise administrators for governing agentic workflows: allowed models, required permissions, workflow approval gates, and reuse policies. By the end, you'll be able to propose a governance baseline for your team.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to a GitHub organisation, **or** you are working with a sandbox org where you can explore settings freely.
- You are familiar with GitHub Actions permissions from [Your First Workflow](07-your-first-workflow.md).

## Steps

### Understand what governance covers

Governance for agentic workflows sits at three levels:

| Level | What you control |
|---|---|
| **Workflow file** | Permissions, model choice, safe-output limits, cost caps |
| **Repository** | Which workflows can run, Actions permissions, required reviewers for workflow files |
| **Organisation / Enterprise** | Allowed models, Actions policies, CODEOWNERS rules, audit log access |

Start from the innermost ring (the workflow file) and work outward.

### Review workflow-level controls you already have

Every `.md` workflow file can enforce its own constraints in the YAML frontmatter. A governance-hardened workflow sets explicit values rather than relying on defaults:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  contents: read
  issues: write
model: gpt-4o
max-ai-credits: 5
max-daily-ai-credits: 10
timeout-minutes: 10
safe-outputs:
  create-issue: limit: 1
---
```

Each field serves a governance purpose:

- **`permissions`** — least-privilege; grant only what the workflow writes.
- **`model`** — pin the model so a future default change does not silently alter behaviour or cost.
- **`max-ai-credits` / `max-daily-ai-credits`** — spending caps from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- **`safe-outputs` limits** — prevent runaway writes if the agent loops unexpectedly.
- **`timeout-minutes`** — hard ceiling on wall-clock time.

> [!TIP]
> Pin the model name explicitly. When your organisation upgrades the default model, pinned workflows are unaffected until you consciously review and re-pin them.

### Set repository-level policies

In your practice repository, navigate to **Settings → Actions → General** and review these settings:

1. **Actions permissions** — set to _Allow select actions and reusable workflows_ to prevent arbitrary third-party actions.
2. **Workflow permissions** — set the default GITHUB_TOKEN scope to _Read repository contents and packages permissions_. Workflows that need write access should request it explicitly in their frontmatter.
3. **Required reviewers for deployments** — if your workflow posts to a production channel (Slack, issue tracker, external API), consider adding a protection rule.

> [!NOTE]
> Repository settings apply to all GitHub Actions workflows, not just agentic ones. Changes here affect every workflow file in the repository.

### Protect workflow files with CODEOWNERS

Agentic workflow files contain the prompt that drives AI behaviour. Treat them like code that touches security boundaries.

Add a `CODEOWNERS` entry so that any changes to `.github/workflows/*.md` require approval from a trusted team:

```
# Require review for all agentic workflow definitions
.github/workflows/*.md   @your-org/workflow-owners
.github/workflows/*.lock.yml   @your-org/workflow-owners
```

Commit this file to `.github/CODEOWNERS` (or `CODEOWNERS` at the repo root).

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/` and click **Add file → Create new file**.
2. Name the file `CODEOWNERS`.
3. Paste the snippet above, replacing `@your-org/workflow-owners` with a real team slug.
4. Click **Commit new file**.

</details>

### Review organisation-level model policies

On GitHub Enterprise Cloud, organisation administrators can restrict which AI models are available to agentic workflows. To check or set this:

1. Go to your organisation page and click **Settings**.
2. Under **Copilot**, review the **Policies** tab.
3. If a model allowlist is configured, only models on the list will be accepted at compile time.

If a workflow's `model:` field names a model that is not on the allowlist, `gh aw compile` will report a validation error. This is the intended behaviour — it surfaces policy violations before a workflow ever runs.

> [!NOTE]
> Model allowlists are a GitHub Enterprise Cloud feature. On GitHub Free or GitHub Team, any supported model can be used.

### Draft a governance baseline

A governance baseline is a short document your team agrees on before rolling out agentic workflows broadly. It does not need to be complex. A one-page checklist is enough to start.

Draft yours by answering these questions in a new GitHub issue in your practice repository:

1. Which permissions should every agentic workflow request as a minimum? Which permissions should require explicit justification?
2. Which models are approved? Who approves new ones?
3. What is the per-run and per-day AIC ceiling for unattended workflows?
4. Who reviews changes to `.github/workflows/*.md` files?
5. How often will you review the audit log (from [Audit and Monitor](25-audit-and-observability.md)) for unexpected activity?

> [!TIP]
> You don't need to answer every question perfectly on day one. A baseline with five agreed points beats a perfect policy that never ships.

## ✅ Checkpoint

- [ ] You can name at least three frontmatter fields that serve a governance purpose and explain each one
- [ ] You set the default Actions workflow permissions to read-only in your practice repository's settings
- [ ] You created or updated a `CODEOWNERS` file that requires review for `.github/workflows/*.md` changes
- [ ] You checked your organisation's Copilot policy settings (or noted that you're on a plan where they don't apply)
- [ ] You drafted at least three points of a governance baseline as a GitHub issue in your practice repository

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose your next path, or explore the [Governance Guide](https://github.github.com/gh-aw/guides/governance/) in the gh-aw docs for deeper policy reference.
<!-- /journey -->

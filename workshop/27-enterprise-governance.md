<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Deploying agentic workflows at scale means more than creating individual automations — it means putting the right policies, approval gates, and visibility controls in place so every team can ship confidently._

## 🎯 What You'll Do

You'll learn how to configure organisation-wide policies that control who can run agentic workflows, require human reviewers before AI outputs are published, and share approved workflow templates so teams start from a known-good baseline.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and understand workflow run artifacts.
- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and have AIC guardrails in place.
- You have **Owner** or **Admin** access to your GitHub organisation. (Read-along mode is fine if you don't — the concepts apply either way.)

> [!NOTE]
> This step applies equally to **GitHub Enterprise Cloud (GHEC)** and **GitHub Enterprise Server (GHES) 3.12+** organisations. `github.com` personal accounts can follow along, but organisation-level policies require org ownership.

## Steps

### Understand the three governance levers

Before touching settings, map the levers available to you:

| Lever | Where it lives | What it controls |
|---|---|---|
| **Actions policies** | Org → Settings → Actions → General | Which workflows can run and which actions are allowed |
| **Environment protection rules** | Repo → Settings → Environments | Require reviewers or a time delay before a workflow can deploy or write |
| **Workflow templates** | Org `.github` repo → `workflow-templates/` | Reusable starting-point workflow files discoverable in the Actions tab |

Start by reviewing the current Actions policy for your organisation.

### Review your organisation's Actions policy

1. Navigate to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Review the **Actions permissions** setting. For enterprise-managed orgs, your GitHub administrator may have locked this at the enterprise level.

> [!TIP]
> A safe starting policy is **Allow enterprise actions and reusable workflows** with an explicit allowlist for the `gh-aw` runner action. This limits exposure if an untrusted workflow is triggered.

### Add an environment protection rule

Environment protection rules let you require a human reviewer before an agentic workflow can post a comment, create a PR, or push code — valuable for workflows that write to production repositories.

1. In the repository where your workflow runs, click **Settings** → **Environments**.
2. Click **New environment** and name it `agentic-reviewed`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add yourself or a teammate as a required reviewer.
4. Save the environment.
5. Open your workflow's `.md` file (for example, `daily-status.md`) and add an `environment:` key to the frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
environment: agentic-reviewed
permissions:
  issues: write
  pull-requests: write
---
```

1. Compile and push:

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "Add environment protection rule"
git push
```

<details>
<summary>🖥️ GitHub UI alternative for editing the workflow file</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to edit.
3. Add `environment: agentic-reviewed` to the frontmatter block.
4. Click **Commit changes**.

Note: You still need to run `gh aw compile` locally or in a Codespace to regenerate the `.lock.yml` after editing the `.md` file.

</details>

The next time the workflow runs, GitHub will pause before the environment step and notify reviewers. Only after a reviewer approves will the run continue.

### Publish a reusable workflow template

Workflow templates let teams start from an approved baseline rather than writing from scratch.

1. Open (or create) the special `.github` repository in your organisation.
2. Inside it, create the directory `workflow-templates/`.
3. Add two files:
   - `agentic-status-report.md` — your `daily-status.md` trimmed down with placeholder values.
   - `agentic-status-report.properties.json`:

```json
{
  "name": "Agentic Status Report",
  "description": "Daily AI-powered status report using gh-aw.",
  "iconName": "octicon-workflow",
  "categories": ["Automation", "AI"]
}
```

1. Commit and push. Any repo in your org will now find your template under **Actions** → **New workflow** → **By your organisation**.

## ✅ Checkpoint

- [ ] You reviewed your organisation's Actions permissions policy and can describe the current setting
- [ ] You created an `agentic-reviewed` environment with at least one required reviewer
- [ ] You added `environment: agentic-reviewed` to a workflow's frontmatter and recompiled
- [ ] You triggered the workflow and confirmed GitHub paused for reviewer approval
- [ ] You created (or identified the location of) the `workflow-templates/` directory in your org's `.github` repo
- [ ] You can explain what the `properties.json` file does and where templates appear in the UI

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

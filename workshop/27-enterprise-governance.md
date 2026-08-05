<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _A single workflow running well is a win — dozens of workflows running consistently, safely, and within policy is how teams make agentic automation stick._

## 🎯 What You'll Do

You'll apply the key governance controls for agentic workflows at org or enterprise scale: policy guards, approved model lists, reusable workflow libraries, and an audit hygiene checklist. By the end you'll have a lightweight governance plan you can share with your team or administrator.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are comfortable editing workflow frontmatter and running `gh aw compile`.
- _(GHES/GHEC users)_ Your administrator has confirmed Copilot Enterprise is enabled for your org.

## Steps

### Understand the governance surface

Agentic workflows inherit GitHub Actions' permission and security model, then add a few new controls unique to the Copilot agent layer.

The table below maps each risk to the right control:

| Risk | Control |
|---|---|
| Workflow writes to unintended resources | `permissions:` in frontmatter, scoped to the minimum needed |
| Agent uses an unapproved model | Org model policy in GitHub settings, plus `model:` in frontmatter |
| Runaway spend | `max-ai-credits` and `max-daily-ai-credits` (see [Step 26](26-manage-costs-and-budgets.md)) |
| Stale or inconsistent workflow definitions | Centralised library with [reusable workflows](18-share-and-reuse.md) |
| No audit trail | `gh aw logs` and org-level audit log (see [Step 25](25-audit-and-observability.md)) |

### Lock down permissions in every workflow

The principle of least privilege applies to agentic workflows just as it does to classic Actions jobs. Start with `permissions: {}` and add only what the task genuinely needs.

Open your workflow file and confirm the frontmatter contains an explicit `permissions` block:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
max-ai-credits: 500
---
```

The `contents: read` scope lets the agent read repository files. `issues: write` allows it to open or update issues. Remove any scope that your task brief does not exercise.

> [!TIP]
> The [gh-aw permissions reference](https://github.github.com/gh-aw/reference/permissions/) lists every scope with its effect on the agent's available tools.

### Enforce an approved-model policy

Some teams standardise on a specific model for cost predictability or compliance. You can lock the model in the workflow frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
model: copilot/gpt-4o
permissions:
  contents: read
  issues: write
---
```

If your organisation has configured an approved-model allow list in GitHub settings, workflows that request an unapproved model are blocked before the agent starts — compile still succeeds, but the run fails with a policy error. Test your chosen model against that list before rolling out to a team.

<details>
<summary>🖥️ Check the approved-model list in the GitHub UI</summary>

1. Go to your organisation on **github.com** and click **Settings**.
2. In the left sidebar, under **Copilot**, click **Policies**.
3. Find the **Allowed models** section. Any model listed there is available to agentic workflows in your org.

</details>

### Build a shared workflow library

Once you have one well-governed workflow, replicate the pattern using [reusable workflows](18-share-and-reuse.md). Store canonical workflow definitions in a central repository (often `.github` or a dedicated `workflows-library` repo) and import them in each team repo.

This approach means:
- Permission and budget controls are defined once and inherited everywhere.
- Security or compliance updates propagate automatically on the next compile.
- New teams start from a tested, policy-compliant template instead of a blank file.

> [!NOTE]
> Read the [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/) for a detailed pattern covering multi-repo deployment, required reviewers, and CODEOWNERS integration.

### Run a governance health check

Before sharing your workflows with a team, confirm:

1. Every workflow has an explicit `permissions:` block with no wildcard scopes.
2. `max-ai-credits` and `max-daily-ai-credits` are set in every scheduled workflow.
3. The `model:` field is set explicitly and matches the org's approved-model policy.
4. Secrets are stored in repository or org secrets, not in the task brief.
5. Workflow files are reviewed via pull request before merge.

Compile after any frontmatter change to catch errors early:

```bash
gh aw compile
```

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes an explicit `permissions:` block scoped to the minimum required
- [ ] You set `model:` explicitly and confirmed it is on your org's approved-model list
- [ ] You reviewed the governance health-check list and resolved any gaps
- [ ] You can describe in plain terms how your org's cost, model, and permission policies apply to agentic workflows

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

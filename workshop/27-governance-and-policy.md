<!-- page-journey: all -->
<!-- page-adventure: enterprise -->
# Govern Agentic Workflows Across Your Organisation

> _AI automation amplifies whatever governance you already have — so putting guardrails in place early is far easier than retrofitting them later._

## 🎯 What You'll Do

You will apply organisation-level governance controls to your agentic workflows: required reviewers before a workflow runs against sensitive repositories, deployment environment protection rules, and a workflow catalog that makes approved patterns reusable. By the end, any workflow you publish to your organisation will pass through a human approval gate and be discoverable by your teammates.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have Owner or Admin rights in a GitHub organisation (GHEC or GHES 3.12+).
- If your organisation uses GitHub Enterprise Server, confirm with your admin that [Deployment Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) are enabled on your instance.

## Steps

### Understand the governance model

Agentic workflows run inside standard GitHub Actions jobs. That means every Actions governance primitive — [environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment), required reviewers, branch protection, and [Actions policies](https://docs.github.com/en/organizations/managing-github-actions-for-organizations-in-your-organization) — applies directly to them.

The gh-aw governance guide at [github.github.com/gh-aw/guides/governance/](https://github.github.com/gh-aw/guides/governance/) covers the full policy surface. The three most impactful controls are:

| Control | What it does |
|---|---|
| **Required reviewers on a deployment environment** | A named person must approve before the job runs |
| **Actions policy: allowed actions** | Restricts which actions and extensions can run in the org |
| **Workflow catalog** | Centralises approved patterns so teams reuse rather than reinvent |

### Create a protected deployment environment

1. In your practice organisation on GitHub, open **Settings → Environments**.
2. Click **New environment** and name it `agentic-production`.
3. Under **Required reviewers**, add yourself (or a teammate) as a required approver.
4. Click **Save protection rules**.

> [!NOTE]
> On GitHub Enterprise Server, environment support depends on your GHES version and the Actions policy configured by your admin. Check **Admin → Actions** in your enterprise settings if the **Environments** option is not visible.

### Wire the environment into your workflow frontmatter

Open `daily-status.md` in your practice repository and add the `environment:` field to the frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
environment: agentic-production
permissions:
  contents: read
  issues: write
---
```

Commit the change. Then recompile:

```bash
gh aw compile
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to edit the file.
3. Add `environment: agentic-production` to the frontmatter block.
4. Click **Commit changes**.
5. If you are on the UI path, skip the `gh aw compile` step — the next workflow run will use the updated file and Actions will enforce the environment gate.

</details>

### Trigger a manual run and approve it

1. Open **Actions → Daily Status Report** in your repository.
2. Click **Run workflow → Run workflow**.
3. The run will pause at the `agentic-production` environment gate and show **Waiting for review**.
4. Click **Review deployments**, select `agentic-production`, and click **Approve and deploy**.
5. The job proceeds. Verify it completes successfully.

### Publish an approved workflow to your organisation catalog

Organisation catalogs let teams discover and install vetted agentic workflow patterns with a single `gh aw install` command:

```bash
gh aw publish --org <your-org>
```

Any repository in the org can then install the approved version:

```bash
gh aw install <your-org>/daily-status
```

See [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md) for a full walkthrough of the publish and install flow, including versioning and deprecation.

## ✅ Checkpoint

- [ ] You created an `agentic-production` deployment environment with at least one required reviewer
- [ ] You added `environment: agentic-production` to your workflow frontmatter and committed the change
- [ ] A manually triggered run paused at the environment gate and required your approval before proceeding
- [ ] The run completed successfully after approval
- [ ] You can explain how deployment environments, Actions policies, and a workflow catalog together form a governance layer for agentic workflows

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

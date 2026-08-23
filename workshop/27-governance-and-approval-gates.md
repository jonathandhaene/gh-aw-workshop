<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Who Can Run Your Agentic Workflows

> _An agentic workflow that can write issues, labels, and pull requests needs guardrails — governance gives your team confidence that automation only acts when the right people approve it._

## 🎯 What You'll Do

You'll add an [Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) gate to your workflow so that a human reviewer must approve each production run. You'll also add a `concurrency` group to prevent two runs from racing each other, and review the repository-level permission model that controls what your workflow can change.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your repository has at least one workflow that uses `safe-outputs` to write back to GitHub.
- _(Enterprise users)_ Your GitHub administrator can create and configure Environments in your organisation. Check with them if you do not see the **Environments** option in your repository settings.

## Steps

### Understand the risk surface

Agentic workflows can act on your behalf — they can label issues, merge pull requests, or post comments. Without controls, a compromised or misconfigured workflow could make unintended changes.

Three mechanisms work together to limit that risk:

| Mechanism | What it controls |
|---|---|
| `permissions:` in workflow frontmatter | Which GitHub API scopes the workflow token holds |
| GitHub Environments | Who must approve before the workflow's write steps run |
| `concurrency:` in workflow frontmatter | Whether a new run cancels or queues behind an in-progress run |

### Create a protected Environment

1. Open your repository on GitHub and click **Settings** → **Environments**.
2. Click **New environment** and name it `production`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add yourself (or a team).
4. Click **Save protection rules**.

![Screenshot of the Environments settings page with Required reviewers enabled](images/27-environment-required-reviewers.png)

> [!NOTE]
> On GitHub Enterprise Cloud or GitHub Enterprise Server, environments and required reviewers are available for all repository types. On free personal repositories, required reviewers are only available for public repositories.

### Reference the environment in your workflow frontmatter

Open your daily-status workflow file (`.github/workflows/daily-status.md`) and add an `environment:` key:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 9 * * 1-5"
  workflow_dispatch: {}
environment: production
permissions:
  issues: write
  contents: read
---
```

Setting `environment: production` tells GitHub Actions to pause the run at the write phase and wait for a reviewer to click **Approve and deploy** before any `safe-outputs` writes are applied.

<details>
<summary>🖥️ GitHub UI alternative — edit the workflow file</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `environment: production` to the frontmatter block between the `---` fences.
4. Click **Commit changes**.

</details>

After editing, regenerate the lock file:

```bash
gh aw compile
```

### Add a concurrency group

A `concurrency:` key prevents two runs of the same workflow from stepping on each other. Add it to the frontmatter alongside `environment:`:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 9 * * 1-5"
  workflow_dispatch: {}
environment: production
concurrency:
  group: daily-status-${{ github.ref }}
  cancel-in-progress: false
permissions:
  issues: write
  contents: read
---
```

Setting `cancel-in-progress: false` means a new run waits for the current one to finish rather than cancelling it — safer for workflows that write GitHub data.

> [!TIP]
> Use `cancel-in-progress: true` for workflows that only read data. For write workflows, `false` prevents partial updates caused by a cancelled mid-run.

### Review your `permissions:` scope

Check that your frontmatter requests only the minimum permissions your workflow needs. A workflow that only posts issue comments does not need `pull-requests: write`.

Refer to the [gh-aw permissions reference](https://github.github.com/gh-aw/reference/permissions/) for the full list of supported scopes and their effect on the token.

> [!NOTE]
> Enterprise teams using GHES or GHEC can also apply organisation-level required workflows that enforce minimum `permissions:` settings across all repositories. Ask your GitHub administrator whether a policy workflow is already in place before adding redundant restrictions.

### Test the approval gate

Trigger a manual run:

1. Go to **Actions** → **Daily Status Report** → **Run workflow**.
2. Notice the run pauses at the **production** environment gate.
3. Click **Review deployments**, approve the run, then watch it complete.

This is exactly what production runs will look like — every write step waits for a human.

## ✅ Checkpoint

- [ ] You created a `production` environment in your repository settings with at least one required reviewer
- [ ] You added `environment: production` to your workflow's frontmatter and regenerated the lock file
- [ ] You added a `concurrency:` group with `cancel-in-progress: false` to your workflow
- [ ] You reviewed your `permissions:` block and confirmed it requests only the minimum required scopes
- [ ] You triggered a manual run and approved it through the environment gate

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

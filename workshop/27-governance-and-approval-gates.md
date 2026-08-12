<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Who Can Run Your Agentic Workflows

> _Enterprise teams need more than a working workflow — they need control over when and by whom it runs._

## 🎯 What You'll Do

You will add an [environment protection rule](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#required-reviewers) to your agentic workflow so that every run must be approved before it executes. By the end of this step, your workflow will pause at a gate, notify a designated reviewer, and only proceed once they approve.

## 📋 Before You Start

- Your agentic workflow runs successfully (see [Test and Iterate on Your Workflow](12-test-and-iterate.md)).
- You have **Admin** access to your practice repository (required to create environments).
- On **GHES** or **GHEC**: confirm that required reviewers and environment protection rules are enabled for your organization. Ask your admin if unsure.

> [!NOTE]
> GitHub-hosted public repositories do not require a paid plan for environment protection rules. Organization-level environments on GHES 3.12+ and GHEC support the same feature.

## Steps

### Understand why governance matters for agentic workflows

A classic Actions workflow runs code deterministically — you know exactly what it will do. An agentic workflow runs an AI model that makes decisions at runtime: writing comments, opening issues, or pushing files.

Environment protection rules give you a manual approval gate. The workflow queues but the agent step only starts after a reviewer clicks **Approve**.

### Create a protected environment

1. In your practice repository on GitHub, click **Settings → Environments**.
2. Click **New environment** and name it `agentic-production`.
3. Under **Deployment protection rules**, enable **Required reviewers**.
4. Add yourself (or a teammate) as a required reviewer and click **Save protection rules**.

![Environment protection rules showing Required reviewers enabled for agentic-production](images/27-environment-protection-rules.png)

### Point your workflow frontmatter at the environment

Open your workflow file (for example `.github/workflows/daily-status.md`) and add the `environment:` key inside the frontmatter `jobs:` block:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
  workflow_dispatch:
jobs:
  report:
    runs-on: ubuntu-latest
    environment: agentic-production
    permissions:
      issues: write
      contents: read
---
```

The `environment:` key is standard GitHub Actions syntax. `gh aw compile` passes it through unchanged to the generated lock file.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `environment: agentic-production` under the `jobs: report:` block as shown above.
4. Click **Commit changes** and commit directly to your default branch.

</details>

After editing, recompile the lock file so GitHub Actions picks up the change:

```bash
gh aw compile
```

Commit and push both the `.md` and the updated `.lock.yml`.

### Trigger a run and approve it

1. Click **Actions → Daily Status Report → Run workflow → Run workflow**.
2. The run pauses with a yellow **Waiting** badge.
3. Click **Review deployments**, tick `agentic-production`, and click **Approve and deploy**.
4. The agent step starts and the run completes normally.

> [!TIP]
> Every approved run links to the reviewer who authorized it — an immutable audit trail with no extra tooling.

### Fine-tune the policy

Three quick additions strengthen the gate further:

- **Reviewer teams** — add a security or ops team as reviewer instead of individuals, so approvals are never blocked by a single person being unavailable.
- **Wait timer** — add a delay (for example, 15 minutes) between approval and execution to support freeze windows.
- **CODEOWNERS** — add `.github/workflows/` to `.github/CODEOWNERS` so changes to workflow files require a code review before they can be merged.

## ✅ Checkpoint

- [ ] You created an environment named `agentic-production` with at least one required reviewer
- [ ] You added `environment: agentic-production` to your workflow frontmatter
- [ ] You ran `gh aw compile` and pushed the updated `.lock.yml`
- [ ] You triggered a manual run and approved it through the environment gate
- [ ] You can explain why an approval gate is valuable for AI-powered workflows in an enterprise setting

<!-- journey: all -->
Want to explore another advanced topic? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

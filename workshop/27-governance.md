# Govern Agentic Workflows Across Your Organisation

> _AI-powered workflows that write code, open PRs, and post comments need guardrails — this step shows you how to set them at the organisation level._

## 🎯 What You'll Do

You'll apply org-level controls to your agentic workflow repository: required reviewers before a workflow can run in production, branch-protection rules that prevent unreviewed AI writes, and a shared policy document your team can reference. By the end, you'll know exactly what stands between a triggered workflow and a real write to your repository.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or arrived here from [What's Next? Keep Exploring](14-next-steps.md).
- You have admin or owner access to your repository (or a practice organisation). Read-only access is enough to follow along conceptually.

## Steps

### Understand the three governance layers

Agentic workflows inherit the full GitHub security model. Think of governance as three layers stacked on top of each other:

| Layer | What it controls | Where you set it |
|-------|-----------------|------------------|
| **Workflow permissions** | What the `GITHUB_TOKEN` can read and write | `permissions:` block in frontmatter |
| **Safe outputs** | Which write operations the agent is allowed to emit | `safe-outputs:` block in frontmatter |
| **Environment protection rules** | Who must approve a workflow run before it touches a protected target | Repository or org **Environments** settings |

You have already seen the first two layers in earlier steps. This step focuses on the third.

### Create a protected environment for production writes

Environments let you require a human review gate before a workflow job runs. This is especially useful for agentic workflows that commit, open PRs, or post to an issue tracker.

**In the GitHub UI:**

1. Go to your repository on GitHub.
2. Click **Settings** → **Environments** → **New environment**.
3. Name it `production`.
4. Under **Deployment protection rules**, enable **Required reviewers** and add yourself or a team.
5. Click **Save protection rules**.

![Environment protection rules settings page](images/27-environment-protection.png)

> [!NOTE]
> Free personal accounts support environment protection rules only on public repositories. If you are working privately, you will still see the **Environments** UI but the reviewer requirement will not be enforced unless your plan includes private environment protection.

### Wire the environment into your workflow frontmatter

Reference the `production` environment in your workflow's frontmatter so the run pauses for approval before the agent can emit any safe output:

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
  - add_comment
---
```

After editing, compile to regenerate the lock file:

```bash
gh aw compile
```

<details>
<summary>🖥️ GitHub UI alternative for editing the workflow file</summary>

1. Navigate to your workflow file in the repository (for example `.github/workflows/daily-status.md`).
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `environment: production` to the frontmatter between the `---` fences.
4. Click **Commit changes**.

Note: the lock file (`*.lock.yml`) will be regenerated automatically the next time `gh aw compile` runs in your CI, or you can trigger the compile step manually.

</details>

### Set branch-protection rules for the default branch

A review gate on the environment stops individual runs. Branch-protection rules stop unreviewed commits from landing on `main` regardless of how they were created.

**In the GitHub UI:**

1. Go to **Settings** → **Branches** → **Add branch ruleset** (or **Add classic branch protection rule**).
2. Apply the rule to `main`.
3. Enable **Require a pull request before merging** and set at least one required reviewer.
4. Optionally enable **Require status checks to pass** and add your CI workflow.
5. Click **Create**.

With this in place, even if an agentic workflow is misconfigured to push directly to `main`, the push will be rejected.

### Document your team's workflow policy

Governance works best when it is written down. Create a short policy file at `.github/agentic-workflow-policy.md` in your repository. A minimal policy covers:

- **Approved safe outputs**: list the write operations your team permits (e.g., `add_comment`, `create-pull-request`).
- **Environment approval**: who is authorised to approve the `production` gate.
- **Token scope ceiling**: the maximum `permissions:` any workflow in this repo may request.
- **Incident response**: how to disable a runaway workflow (repository **Actions** tab → workflow → **Disable workflow**).

You do not need to create this file now — even a bullet-point comment in your workflow brief is a good start.

## ✅ Checkpoint

- [ ] You can describe the three governance layers (permissions, safe outputs, environment protection) and what each one controls.
- [ ] You created a `production` environment in your repository settings and added at least one required reviewer.
- [ ] You added `environment: production` to your workflow frontmatter and recompiled (or committed the change via the UI).
- [ ] You enabled or reviewed branch-protection rules on your default branch.
- [ ] You know how to disable a workflow from the GitHub Actions tab if a run behaves unexpectedly.

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

<!-- journey: all -->
<!-- /journey -->

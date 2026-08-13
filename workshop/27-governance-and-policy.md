# Govern Your Agentic Workflows at the Organisation Level

> _Autonomous workflows that write to repositories need guardrails — this step shows you how to enforce them before a production incident makes you wish you had._

## 🎯 What You'll Do

Configure environment protection rules, required reviewers, and branch policies so your agentic workflows follow the same governance your team already applies to human pull requests. By the end you'll have a deployment environment that acts as a gate between your workflow's autonomous actions and your main branch.

## 📋 Before You Start

- You have a workflow that creates pull requests or writes to a repository from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or any earlier step.
- You have **Admin** access to the repository (needed to create environments and protection rules).
- Enterprise users: check whether your organisation has a policy that already blocks creating environments — if so, ask an org admin to enable it for your practice repository first.

## Steps

### Understand what "governance" means for agentic workflows

An agentic workflow is an autonomous actor. It can open pull requests, create issues, post comments, and push files — all without a human at the keyboard. Left unguarded, a misbehaving or compromised workflow could:

- Merge code into `main` without review
- Post hundreds of issues from a tight loop
- Write credentials to a public file if something goes wrong in the agent brief

Three controls work together to prevent these outcomes:

| Control | What it does |
|---|---|
| **Environment protection rules** | Require a human approval before the workflow job can access environment secrets or perform write actions |
| **Branch protection rules** | Require a pull request and passing status checks before any code lands on `main` |
| **Minimal `permissions:` + `safe-outputs:`** | Limit what the workflow _can_ do at the API level, regardless of what the agent attempts |

You already set `permissions:` and `safe-outputs:` in earlier steps. This step adds the environment layer on top.

### Create a deployment environment

1. Go to your practice repository on GitHub.
2. Click **Settings** → **Environments** → **New environment**.
3. Name it `agentic-workflows` and click **Configure environment**.
4. Under **Required reviewers**, click **Add required reviewers** and add yourself (or a teammate).
5. Leave **Wait timer** at 0 for now.
6. Click **Save protection rules**.

> [!NOTE]
> The free personal plan supports protected environments for public repositories. For private repositories on a free plan, environment protection rules require a paid plan. If you hit this limit, read the note at the end of this step for an alternative approach.

### Add the environment to your workflow

Open your agentic workflow `.md` file and add an `environment:` key to the frontmatter:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: '0 8 * * 1-5'
  workflow_dispatch:

permissions:
  contents: read
  issues: write

environment: agentic-workflows

safe-outputs:
  - create-issue-comment
---
```

This tells GitHub Actions to require environment approval before your workflow job starts. Any run triggered by the schedule will pause at the environment gate until a reviewer approves it.

Compile after saving:

```bash
gh aw compile
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow `.md` file in your repository.
2. Click the **pencil icon (✏️)** to edit the file.
3. Add `environment: agentic-workflows` to the frontmatter block (after the `permissions:` section).
4. Click **Commit changes**.

You will still need a terminal to run `gh aw compile`, or you can push the file and rely on the next scheduled run to surface any compile errors in the Actions log.

</details>

### Enable branch protection on `main`

If your workflows write files or open pull requests targeting `main`, add a branch ruleset:

1. Go to **Settings** → **Branches** → **Add rule** (classic) or **Rulesets** → **New ruleset**.
2. Set the **Branch name pattern** to `main`.
3. Enable **Require a pull request before merging** and set **Required approvals** to `1`.
4. Enable **Require status checks to pass** (add any CI check that already runs).
5. Click **Create** (or **Save changes**).

> [!TIP]
> Branch rules complement environment rules. The environment gate controls when the workflow _starts_; the branch rule controls what the workflow can _land_ on `main`. Both are useful.

### Check concurrency settings

Governance also means preventing overlapping runs. Add a `concurrency:` block to your workflow frontmatter so only one instance runs at a time:

```yaml
concurrency:
  group: ${{ github.workflow }}
  cancel-in-progress: false
```

`cancel-in-progress: false` ensures a queued run waits instead of being cancelled. For a scheduled daily workflow this is usually the right choice — you want the run to happen, just not twice simultaneously.

Compile again after saving:

```bash
gh aw compile
```

### Verify the gate works

Trigger a manual run via **Actions** → your workflow → **Run workflow**. The run should pause at the environment step and show a yellow **Waiting** badge. Click **Review deployments**, approve the run, and watch it complete.

If you see a "waiting" state but no approval prompt, check that the current actor (the user who triggered the run) is not the same account listed as the required reviewer — GitHub does not allow self-approval on protected environments.

> [!NOTE]
> On GitHub Enterprise Server (GHES) and GitHub Enterprise Cloud (GHEC), your organisation admin can also enforce environment protection rules at the _organisation_ level using **Policies → Actions → Required environments**. This means all repositories in the org that run agentic workflows will inherit the same gate automatically — a strong compliance posture for regulated industries.

## ✅ Checkpoint

- [ ] You created an `agentic-workflows` deployment environment in your repository settings
- [ ] You added at least one required reviewer to that environment
- [ ] Your workflow frontmatter includes `environment: agentic-workflows`
- [ ] `gh aw compile` passes with no errors after the frontmatter change
- [ ] A manual run shows a yellow **Waiting** badge and pauses for approval
- [ ] You approved the run and it completed successfully
- [ ] Your `main` branch has a protection rule requiring a pull request before merging

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

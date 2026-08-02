<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _As agentic workflows multiply across teams, you need guardrails that scale — approval gates, branch protections, and organisation-wide policies that keep AI automation safe without slowing teams down._

## 🎯 What You'll Do

You will configure three layers of governance: required reviewers for sensitive runs, branch-protection rules that prevent unapproved workflow changes, and an organisation-level policy that limits which engines teams can use. You will finish with a reproducible baseline you can roll out to other repositories.

## 📋 Before You Start

- You have a working agentic workflow from previous steps (the `daily-status` workflow works well).
- You understand `safe-outputs` and `permissions:` — see [Make Your Workflows Resilient to Failure](22-error-handling-and-resilience.md).
- You have repository **Admin** access. Organisation policies require **Organisation Owner** access.

## Steps

### Add required reviewers to a workflow environment

GitHub Actions [environments](https://docs.github.com/actions/deployment/targeting-different-environments/using-environments-for-deployment) let you require a human sign-off before a job proceeds.

1. Go to your repository → **Settings** → **Environments** → **New environment**.
2. Name the environment `agentic-production` and click **Configure environment**.
3. Under **Required reviewers**, add yourself or a team.
4. Click **Save protection rules**.

Now reference the environment in your workflow frontmatter:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 9 * * 1-5"
  workflow_dispatch:
jobs:
  run:
    runs-on: ubuntu-latest
    environment: agentic-production   # <-- add this line
permissions:
  issues: write
  contents: read
---
```

Compile after editing:

```bash
gh aw compile
```

On the next manual run, the job will pause at the environment gate. A reviewer sees a yellow banner in the Actions UI and clicks **Review deployments** to approve or reject.

> [!NOTE]
> The `environment:` key is standard GitHub Actions — it works identically in agentic workflows. No gh-aw-specific syntax is required.

### Protect the workflow file with a branch rule

Prevent anyone from pushing changes to `.github/workflows/*.md` without a pull request review.

1. Go to **Settings** → **Branches** → **Add branch protection rule**.
2. Set the branch pattern to `main`.
3. Enable **Require a pull request before merging** with at least `1` approval.
4. Enable **Require review from Code Owners**.
5. Click **Create**.

Create a `.github/CODEOWNERS` file to route workflow reviews automatically:

1. In your repository, click **Add file** → **Create new file** and name it `.github/CODEOWNERS`.
1. Add:

```
# Agentic workflow definitions require team sign-off
.github/workflows/*.md        @your-org/your-team
.github/workflows/*.lock.yml  @your-org/your-team
```

1. Click **Commit new file**.

> [!TIP]
> Protecting both `*.md` and `*.lock.yml` ensures compiled lock files cannot be changed independently of the source definition.

### Set an organisation-level engine policy

> [!NOTE]
> Organisation-level engine policies require GitHub Enterprise Cloud or GitHub Enterprise Server 3.14+. Personal account users can skip this step.

Enterprise organisations can restrict which AI engines teams are permitted to use, blocking unapproved external models.

1. Go to your organisation → **Settings** → **Copilot** → **Policies**.
2. Under **Agentic Workflow Engine Policy**, choose **Allowed engines**.
3. Add `copilot` and remove any engines your security team has not approved.
4. Click **Save**.

Workflows that specify a disallowed engine will fail at compile time with a policy violation error.

### Verify your governance setup

Trigger a manual run from the **Actions** tab. The job should pause at the environment gate. Approve it and confirm the run completes successfully.

![Environment approval gate in the GitHub Actions UI](images/27-governance-approval-gate.svg)

## ✅ Checkpoint

- [ ] You created an `agentic-production` environment with at least one required reviewer
- [ ] Your workflow frontmatter includes `environment: agentic-production`
- [ ] `gh aw compile` passes and the updated `.lock.yml` is committed
- [ ] A manual run paused at the environment gate and required approval before proceeding
- [ ] You created `.github/CODEOWNERS` routing workflow reviews to your team
- [ ] Your `main` branch requires at least one pull request approval before merging
- [ ] You can explain the difference between environment gates (runtime control) and branch protection (change control)

**Next:** [Workshop Complete — What to Build Next](14-next-steps.md)

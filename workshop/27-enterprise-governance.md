<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Shipping an agentic workflow is easy; keeping dozens of them safe, compliant, and on-budget as your team grows is the real discipline._

## 🎯 What You'll Do

You'll apply three layers of enterprise governance to your agentic workflows: required reviewers for workflow file changes, organisation-level spend limits, and a protected branch policy that prevents unreviewed workflow edits from reaching production. By the end, you'll have a reproducible setup you can apply to any repository in your organisation.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to at least one GitHub organisation (or a practice organisation).
- _(GitHub Enterprise Cloud or GitHub Enterprise Server users)_ Your enterprise admin has enabled organisation-level Actions policies.

## Steps

### Understand the three governance layers

Enterprise governance for agentic workflows operates at three levels:

| Layer | What it controls | Where you set it |
|---|---|---|
| **Branch protection** | Who can merge changes to `.github/workflows/` | Repository **Settings → Branches** |
| **Required reviewers** | A named person or team must approve before a workflow runs | Actions environment settings |
| **Organisation spend policy** | Maximum monthly AIC across all repositories | Organisation **Settings → Billing** |

Each layer is independent. Use all three for production workflows; use at least branch protection for any shared repository.

### Protect the workflows directory with a CODEOWNERS rule

A `CODEOWNERS` file forces a named team to review pull requests that touch workflow files.

In your repository, create or edit `.github/CODEOWNERS` and add:

```text
.github/workflows/   @your-org/workflow-reviewers
```

Replace `your-org/workflow-reviewers` with a real team slug in your organisation. Anyone who opens a pull request modifying a file under `.github/workflows/` will automatically request a review from that team.

<details>
<summary>🖥️ GitHub UI steps</summary>

1. In your repository, click **Add file → Create new file** and name it `.github/CODEOWNERS`.
2. Add the line above, replacing the team slug.
3. Click **Commit new file**.

</details>

> [!NOTE]
> `CODEOWNERS` rules only enforce reviews when branch protection also requires at least one approving review. Set up both together.

### Enable required reviews on your main branch

1. Go to your repository **Settings → Branches**.
2. Under **Branch protection rules**, click **Add rule** (or edit the rule for `main`).
3. Enable **Require a pull request before merging**.
4. Set **Required number of approvals** to at least **1**.
5. Enable **Require review from Code Owners**.
6. Click **Save changes**.

With both rules in place, no workflow change reaches `main` without a code-owner approval.

![Branch protection rule showing CODEOWNERS review requirement](images/27-branch-protection-codeowners.png)

### Add a required reviewer environment

For extra control on sensitive workflows, create a deployment environment that gates runs behind a manual approval.

1. Go to **Settings → Environments** and click **New environment**.
2. Name it `workflow-production`.
3. Enable **Required reviewers** and add your team.
4. Click **Save protection rules**.

In your workflow frontmatter, reference the environment:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
environment: workflow-production
---
```

> [!NOTE]
> The `environment:` key follows standard GitHub Actions semantics. An agentic workflow with a required-reviewer environment will pause at the start of each run until a reviewer approves it in the Actions UI.

### Set an organisation-level AIC spend limit

1. Go to your **Organisation Settings → Billing**.
2. Find the **Copilot** section and locate **AI Credits spending limit**.
3. Set a monthly cap appropriate for your team.

This limit applies across all repositories in the organisation. Individual workflow-level limits (`max-ai-credits`, `max-daily-ai-credits`) still apply as finer-grained guardrails.

> [!TIP]
> Set the organisation limit slightly higher than the sum of your individual workflow limits. This gives you a buffer for one-off manual runs while still protecting against runaway usage.

### Audit your governance posture

Run a quick audit to confirm all three layers are in place:

```bash
gh api repos/:owner/:repo/branches/main/protection --jq '.required_pull_request_reviews'
gh api repos/:owner/:repo/contents/.github/CODEOWNERS --jq '.content' | base64 -d
```

Review the output. You should see `require_code_owner_reviews: true` and your CODEOWNERS entry for `.github/workflows/`.

## ✅ Checkpoint

- [ ] `.github/CODEOWNERS` includes a rule for `.github/workflows/` pointing to a real team
- [ ] Branch protection on `main` requires at least one approving review from Code Owners
- [ ] A test pull request modifying a workflow file shows the expected code-owner review request
- [ ] You created a `workflow-production` environment with at least one required reviewer
- [ ] At least one of your workflow files references `environment: workflow-production` in its frontmatter
- [ ] You set or confirmed an organisation-level AIC spend limit in the billing dashboard
- [ ] You can explain the difference between a branch-protection rule and a required-reviewer environment

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

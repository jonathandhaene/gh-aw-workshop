# Govern Agentic Workflows Across Your Organisation

> _Shipping an agentic workflow to one repository is a proof of concept — rolling it out safely across an enterprise requires policy controls, required reviews, and a repeatable approval process._

## 🎯 What You'll Do

You will apply organisation-level governance to your agentic workflow: protect the workflow file from unreviewed changes, require a human approval before any scheduled or event-driven agentic run can write to your repository, and document a lightweight change-management policy your team can follow.

## 📋 Before You Start

- You have a working agentic workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to the repository (or an org admin can apply the settings on your behalf).
- Your repository is part of a GitHub organisation (GHEC or GHES).

## Steps

### Protect the workflow definition file

Your agentic workflow lives in `.github/workflows/`. Treat it like application code — changes should go through a pull request and a review.

1. In your repository, go to **Settings** → **Branches**.
2. Click **Add branch ruleset** (or **Add rule** on older GHES versions).
3. Set **Target branches** to `main` (or your default branch).
4. Enable **Require a pull request before merging** and set **Required approvals** to at least `1`.
5. Enable **Require review from Code Owners**.
6. Click **Save changes**.

Next, create a `CODEOWNERS` file so that every change to `.github/workflows/` routes to your platform team:

```text
# .github/CODEOWNERS
.github/workflows/ @your-org/platform-team
```

Commit this file to your default branch via a pull request.

<details>
<summary>🖥️ GitHub UI alternative — create CODEOWNERS</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. Set the file name to `.github/CODEOWNERS`.
3. Paste the content above, replacing `your-org/platform-team` with your real team slug.
4. Click **Commit new file** and open a pull request.

</details>

### Require an environment approval before agentic runs

An **environment** lets you gate any workflow run behind a required reviewer. This is especially valuable for agentic workflows that can open issues, post comments, or push commits.

1. Go to **Settings** → **Environments** → **New environment**.
2. Name it `agentic-production`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add your platform team or yourself.
4. Click **Save protection rules**.

Now reference this environment in your workflow frontmatter so every run must be approved:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: '0 8 * * *'
environment: agentic-production
permissions:
  contents: read
  issues: write
  pull-requests: write
---
```

> [!NOTE]
> When a scheduled run reaches the environment gate, GitHub pauses the workflow and notifies the required reviewers by email. A reviewer clicks **Review deployments** → **Approve** in the Actions UI to let the run proceed.

Compile after editing:

```bash
gh aw compile
```

### Document a change-management policy

A short policy in your repository wiki or a `docs/AGENTIC-WORKFLOWS.md` file helps teammates understand the review expectations. A minimal policy covers:

- **Who can approve** workflow definition changes (the platform team via CODEOWNERS).
- **Who can approve** a production run (the required environment reviewer list).
- **What to check** during review: does the new brief limit scope to what the workflow needs? Are `permissions:` minimal? Is `max-ai-credits` set?
- **How to roll back**: disable the workflow from **Actions** → **<workflow name>** → **…** → **Disable workflow**, then revert the offending commit via a pull request.

> [!TIP]
> Keep the policy short — one page or less. A long policy nobody reads is worse than a short one everyone follows.

## ✅ Checkpoint

- [ ] Branch protection is enabled on your default branch, requiring at least one pull request approval for changes to `.github/workflows/`
- [ ] A `CODEOWNERS` file routes `.github/workflows/` changes to your platform team
- [ ] An `agentic-production` environment exists with at least one required reviewer
- [ ] Your workflow frontmatter references `environment: agentic-production`
- [ ] You can describe the two approval gates your workflow now goes through (code review + environment approval)
- [ ] A change-management policy document (however brief) is accessible to your team

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

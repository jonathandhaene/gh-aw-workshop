<!-- page-journey: enterprise -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows write to your repositories automatically — this step shows you how to keep that power under control at the organisation level._

## 🎯 What You'll Do

You'll configure the organisation-level policy controls that govern how agentic workflows can run across your repositories. By the end, you'll have required approval gates for scheduled AI runs, a CODEOWNERS rule that prevents unreviewed workflow changes, and a clear picture of what your GitHub Enterprise administrator needs to enable or restrict.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) account and can reach an organisation owner or GitHub Enterprise administrator.
- Your practice repository is inside a GitHub organisation (not a personal namespace).

> [!NOTE]
> Some steps in this node require organisation owner or enterprise administrator access. If you only have repository access, read through the sections and note which settings to request from your administrator.

## Require Approval for Workflow Runs from Forks

GitHub Actions can require human approval before running workflows triggered by pull requests from forks. This applies to agentic workflows too — requiring approval prevents untrusted contributors from triggering AI runs that write to your repository.

### Set the approval policy

1. Navigate to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Fork pull request workflows**, choose **Require approval for first-time contributors**.

For high-security repositories, choose **Require approval for all outside collaborators** to require approval on every PR from anyone without write access.

> [!TIP]
> Enterprise teams often start with **first-time contributors** and upgrade to **all outside collaborators** once they confirm the policy does not slow down their trusted contributor community.

## Protect Workflow Source Files with CODEOWNERS

Agentic workflow files (`.github/workflows/*.md` and `.github/workflows/*.lock.yml`) are powerful. An unreviewed change to the Markdown brief or frontmatter can alter what the agent writes. Use a `CODEOWNERS` rule to require a named team to review every workflow edit.

### Add a CODEOWNERS entry

In your practice repository, open or create `.github/CODEOWNERS` and add:

```text
.github/workflows/ @your-org/platform-team
```

Replace `@your-org/platform-team` with the GitHub team that owns your workflow definitions. When this rule is in place, GitHub automatically requests a review from that team whenever someone opens a pull request that touches any file under `.github/workflows/`.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/` and click **Add file** → **Create new file**.
2. Type `CODEOWNERS` as the file name.
3. Add `.github/workflows/ @your-org/platform-team` in the editor.
4. Click **Commit new file**.

</details>

### Pair CODEOWNERS with a branch protection rule

A `CODEOWNERS` entry only has teeth when your default branch requires pull request reviews:

1. Go to **Settings** → **Branches** → **Add branch protection rule**.
2. Set **Branch name pattern** to `main` (or your default branch).
3. Enable **Require a pull request before merging** and check **Require review from Code Owners**.
4. Click **Save changes**.

Workflow source file changes now require an approved review from your platform team before merging.

## Configure Actions Permissions at the Organisation Level

Organisation owners can restrict which repositories are allowed to run GitHub Actions at all, which is a fast way to prevent agentic workflows from running in repositories that should not have AI write access.

1. Go to **Organisation Settings** → **Actions** → **General**.
2. Under **Policies**, choose **Allow select non-GitHub actions and reusable workflows** to limit which external Actions can be used alongside your agentic workflows.
3. Use the **Allow list** to permit only the `gh-aw` action and any other trusted actions your organisation approves.

> [!NOTE]
> On GitHub Enterprise Server, the Actions policies page is under **Enterprise Settings** for administrator-level controls and under **Organisation Settings** for org-level overrides. Ask your GitHub administrator which level your organisation enforces.

## Restrict Self-Hosted Runner Access

If your organisation uses self-hosted runners (covered in [Run Your Agentic Workflow on a Self-Hosted Runner](24-self-hosted-runners.md)), restrict which repositories can access each runner group:

1. Go to **Organisation Settings** → **Actions** → **Runner groups**.
2. Click the runner group your agentic workflows use.
3. Under **Repository access**, select **Selected repositories** and add only the repositories that should use that runner group.

This prevents a repository that should not have AI write access from picking up a runner that carries elevated network or storage permissions.

## ✅ Checkpoint

- [ ] Your organisation's fork pull request approval policy is set to **Require approval for first-time contributors** or stricter
- [ ] `.github/CODEOWNERS` includes an entry covering `.github/workflows/` pointing to a responsible team
- [ ] Your default branch has a protection rule that requires Code Owners review before merging
- [ ] You have reviewed the organisation-level Actions permissions and confirmed the allowed-actions list is appropriate
- [ ] If your organisation uses self-hosted runners, the runner group is restricted to the correct set of repositories
- [ ] You can explain to a teammate why these controls matter for agentic workflows specifically

<!-- journey: enterprise -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

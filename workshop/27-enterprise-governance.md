# Govern Agentic Workflows Across Your Organisation

> _When one team's workflow becomes everyone's workflow, you need guardrails — here's how to set them at the org level._

## 🎯 What You'll Do

Configure organisation-level policies that control which agentic workflows can run, who can approve them, and how to maintain visibility across all repositories. By the end of this step you'll have a repeatable governance model you can hand to your security or platform team.

## 📋 Before You Start

- You have at least one agentic workflow running in a GitHub Actions repository.
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to the GitHub organisation you want to govern. If you are using GHES or GHEC, confirm your enterprise admin can grant this temporarily.

## Steps

### Understand the governance surface

Agentic workflows run as GitHub Actions jobs. Every guardrail that GitHub Actions provides applies to them too: Actions policies, required workflows, branch protection rules, and OIDC token constraints. You do not need a separate governance system — you extend what you already have.

The three layers to configure are:

| Layer | What it controls |
|---|---|
| Organisation **Actions policies** | Which repositories can enable Actions; which actions and reusable workflows are allowed |
| **Required workflows** | Workflows that must pass before a PR can merge, applied org-wide |
| **CODEOWNERS + branch protection** | Who must approve changes to workflow `.md` and `.lock.yml` files |

### Set organisation Actions policies

Navigate to your organisation's settings.

1. Go to **Settings** → **Actions** → **General**.
2. Under **Policies**, choose **Allow select actions and reusable workflows**.
3. In the allow list, add your approved gh-aw workflow source pattern — for example:

   ```
   your-org/approved-workflows/.github/workflows/*.lock.yml@main
   ```

   This means only `.lock.yml` files on `main` from your designated `approved-workflows` repository can be referenced as reusable workflows across the org.

4. Click **Save**.

> [!TIP]
> You do not need to restrict every repository. Start by scoping the policy to repositories that contain production or customer-facing workflows; let developer sandboxes remain open.

### Protect workflow definition files with CODEOWNERS

Agentic workflow definitions live in `.github/workflows/`. Add a `CODEOWNERS` entry so any change to a workflow file requires approval from your platform or security team.

Create or update `.github/CODEOWNERS` in each governed repository:

```
.github/workflows/*.md    @your-org/platform-team
.github/workflows/*.lock.yml    @your-org/platform-team
```

This means every pull request that modifies a workflow definition needs a review from `platform-team` before it can merge.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In the repository on GitHub, click **Add file** → **Create new file** (or click the pencil icon on an existing `CODEOWNERS` file).
2. Set the path to `.github/CODEOWNERS`.
3. Add the lines above, substituting your actual team slug.
4. Click **Commit changes**.

</details>

### Pin workflow permissions at the org level

In **Settings** → **Actions** → **General**, scroll to **Workflow permissions** and select **Read repository contents and packages permissions**. This sets the default `GITHUB_TOKEN` scope to read-only for all new workflows in the org.

Individual workflows that need write access must then explicitly opt in with a `permissions:` block — making elevated access visible in code review rather than inherited silently.

### Enable required status checks for workflow changes

If your organisation uses branch protection on `main`:

1. Go to **Settings** → **Branches** → **main** → **Edit**.
2. Under **Require status checks to pass before merging**, add the CI check that validates your `.lock.yml` files (for example, a job that runs `gh aw compile --validate`).
3. Enable **Require branches to be up to date before merging**.

This ensures no one can merge a modified workflow that has not been compiled and validated.

### Review org-wide workflow activity

To see all agentic workflow runs across repositories from a single place:

1. Go to **github.com/orgs/your-org/actions** (or the equivalent GHES URL).
2. Filter by workflow name to surface all runs of a specific agentic workflow type.
3. Export or share the URL as your org's central workflow activity feed.

For automated reporting, you can build an agentic workflow that queries the Actions API, summarises run outcomes across repositories, and posts a daily digest to a team Slack channel or GitHub Discussion.

## ✅ Checkpoint

- [ ] You configured your organisation's Actions policies to limit which reusable workflow sources are allowed
- [ ] You added a `CODEOWNERS` entry that requires platform team approval for `.md` and `.lock.yml` changes
- [ ] You set the default `GITHUB_TOKEN` permissions to read-only at the org level
- [ ] You enabled a required status check that validates compiled workflow files before merge
- [ ] You can navigate to your organisation's Actions page and filter by workflow name to see cross-repo activity
- [ ] You can explain the three governance layers (Actions policies, required workflows, CODEOWNERS) to a teammate

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

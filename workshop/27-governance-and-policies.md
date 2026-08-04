<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows write to repositories, open pull requests, and call external APIs — giving them that power responsibly means putting clear rules in place before you scale._

## 🎯 What You'll Do

You'll learn how to apply organisation-level guardrails to agentic workflows: restricting which permissions workflows may request, requiring code review before a workflow reaches production, and using [required workflows](https://docs.github.com/en/actions/using-workflows/required-workflows) to enforce a common baseline across all repositories in your org.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or at least [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have **Owner** or **Admin** access to a GitHub organisation — or you can follow along in a personal repository and apply the concepts later.

> [!NOTE]
> This step focuses on GitHub Enterprise Cloud (GHEC) and GitHub Enterprise Server (GHES) features. Most settings are also available on free organisations, but some (required workflows, enterprise policy enforcement) require a Team or Enterprise plan.

## Steps

### Understand what governance covers

When an agentic workflow runs, it acts on behalf of the `GITHUB_TOKEN` your repository provides. Left unchecked, a workflow could:

- Write commits to the default branch without review.
- Create or merge pull requests automatically.
- Call external APIs with secrets it has been granted.

Governance means deciding — deliberately — which of those actions are acceptable, and enforcing those decisions through platform controls rather than trust.

### Review the permissions your workflow requests

Open your workflow's `.md` file and check the `permissions:` block in the frontmatter:

```yaml
---
name: daily-status
on:
  schedule: daily
permissions:
  contents: read
  issues: write
---
```

Apply the **principle of least privilege**: grant only the permissions the workflow actually needs. If your workflow reads repository contents and posts issue comments, `contents: read` and `issues: write` are sufficient — do not add `pull-requests: write` unless the workflow opens PRs.

> [!TIP]
> The [permissions reference](https://github.github.com/gh-aw/reference/permissions/) lists every available permission scope and the tools that require each one.

### Restrict default permissions at the repository level

1. On GitHub, navigate to your repository → **Settings** → **Actions** → **General**.
2. Under **Workflow permissions**, select **Read repository contents and packages permissions**.
3. Click **Save**.

This ensures that any workflow that omits a `permissions:` block gets only read access by default. Explicit permissions in the frontmatter still apply as written.

![Workflow permissions setting in repository Actions settings](images/27-workflow-permissions.png)

### Require pull request review before deploying a new workflow

Never merge a new agentic workflow directly to your default branch without review. A colleague can spot an overly broad `permissions:` block or a prompt that might produce unintended side effects.

To enforce this, add a [branch protection rule](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) on your default branch:

1. Go to **Settings** → **Branches** → **Add branch ruleset** (or **Branch protection rules** on older UI).
2. Target your default branch (e.g. `main`).
3. Enable **Require a pull request before merging** and set **Required approvals** to at least `1`.
4. Enable **Require status checks to pass** — adding your CI lint or compile check as a required status is a good baseline.
5. Click **Create**.

### Apply an org-level policy to cap workflow permissions (GHEC/GHES)

Org owners can prevent any repository from granting workflows more than read access by default:

1. Go to your **Organisation settings** → **Actions** → **General**.
2. Under **Workflow permissions**, select **Read repository contents and packages permissions**.
3. Click **Save**.

Individual repository admins can still grant higher permissions in specific repositories — but they must do so explicitly, which creates an audit trail.

> [!NOTE]
> <details>
> <summary><b>GHES: where to find org-level Actions policy</b></summary>
>
> On GitHub Enterprise Server, the path is the same — **Organisation settings → Actions → General** — but the exact option labels may differ slightly depending on your GHES version. Check your site administrator's documentation if the option is absent; it may require a site-admin policy to unlock it first.
>
> </details>

### Use a required workflow to enforce a baseline (Enterprise plans)

A [required workflow](https://docs.github.com/en/actions/using-workflows/required-workflows) runs automatically on every pull request across selected repositories in your org. You can use one to:

- Validate that every new agentic workflow file passes `gh aw compile`.
- Check that the `permissions:` block is present and follows your naming conventions.

Create a simple required-workflow file in a central `.github` repository in your org:

```yaml
name: Validate agentic workflow
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install gh-aw
        run: gh extension install github/gh-aw
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      - name: Validate changed workflow files
        run: |
          git diff --name-only origin/${{ github.base_ref }}...HEAD \
            | grep '\.github/workflows/.*\.md$' \
            | xargs -I{} gh aw compile --validate {}
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Once saved and configured as a required workflow for your org, every PR that touches a `.github/workflows/*.md` file will be blocked until this check passes.

## ✅ Checkpoint

- [ ] Your workflow's frontmatter includes an explicit `permissions:` block using least-privilege scopes
- [ ] Your repository's default workflow permissions are set to **Read repository contents**
- [ ] Your default branch has a protection rule requiring at least one pull request approval
- [ ] You can describe the difference between repository-level and org-level workflow permission policies
- [ ] _(Enterprise)_ You have located the org-level Actions policy page and noted whether it restricts default permissions

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose another advanced path.
<!-- /journey -->

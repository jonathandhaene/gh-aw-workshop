# Govern Agentic Workflows at the Organisation Level

> _Agentic workflows write to production — so enterprise teams need guardrails that go beyond cost limits._

## 🎯 What You'll Do

You'll configure organisation-level controls that protect your repositories from unintended agentic actions. By the end, you'll have required approval gates for workflow file changes, a CODEOWNERS policy, and a narrowly-scoped `permissions:` block in your frontmatter.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have Owner or Admin access to a GitHub organisation (GHEC or GHES 3.10+).
- You understand what agentic workflow `permissions:` do — see the [Permissions reference](https://github.github.com/gh-aw/reference/permissions/).

> [!NOTE]
> If you are working in a personal repository, you can still follow along — note which settings require org access and return to them when you join an enterprise account.

## Steps

### Protect workflow files with CODEOWNERS

Agentic workflow `.md` files are code. Anyone who edits them changes what your AI agent does. Add a CODEOWNERS rule so every change to `.github/workflows/` requires sign-off.

In your repository, create or edit `.github/CODEOWNERS`:

```
# Require review from the platform team for all agentic workflow files
.github/workflows/ @your-org/platform-team
```

Replace `@your-org/platform-team` with a real GitHub team. Commit and push to the default branch.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository, click **Add file** → **Create new file**.
2. Type `.github/CODEOWNERS` as the file name.
3. Paste the CODEOWNERS rule above.
4. Click **Commit new file**.

</details>

### Require pull request reviews before merging workflow changes

1. In your repository, go to **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit an existing protection rule for your default branch).
3. Under **Require a pull request before merging**, enable it and set **Required approvals** to at least `1`.
4. Check **Require review from Code Owners** — this ties the CODEOWNERS rule you just created to the merge gate.
5. Click **Save changes**.

Now any change to a `.github/workflows/` file must be reviewed and approved before it can take effect.

### Review organisation-level Actions policies (GHEC)

Organisation owners can restrict which repositories may run Actions and which actions are allowed.

1. In your organisation, go to **Settings** → **Actions** → **General**.
2. Under **Policies**, set **Allow select actions and reusable workflows** and allowlist the `gh-aw` runtime action your lock files reference.
3. Under **Runner groups**, verify that self-hosted runner groups are scoped to the correct repositories.

> [!TIP]
> On GHES, these settings live under **Admin console** → **Policies** → **Actions**. The options are the same but the navigation path differs.

### Add a governance comment to your workflow frontmatter

Open one of your agentic workflow files — for example, `.github/workflows/daily-status.md` — and add a `permissions:` block that is scoped to the minimum required:

```yaml
---
name: Daily Status Report
on:
  schedule: daily
permissions:
  contents: read
  issues: write
---
```

A narrow `permissions:` block limits what the AI agent can do even if its brief were manipulated. Avoid `contents: write` unless the workflow must push commits.

Commit and regenerate the lock file:

```bash
gh aw compile
```

## ✅ Checkpoint

- [ ] You added a CODEOWNERS rule that routes `.github/workflows/` changes to a named team or owner
- [ ] You enabled branch protection requiring at least one code owner review before merging workflow file changes
- [ ] You audited your organisation's Actions policy to verify which repositories and actions are allowed (GHEC/GHES)
- [ ] You confirmed your agentic workflow frontmatter uses a minimal `permissions:` block
- [ ] You recompiled the workflow after updating frontmatter

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

<!-- page-journey: all -->

# Govern and Roll Out Agentic Workflows Across Your Team

> _A workflow that works for you alone is a prototype — this step turns it into a shared, trusted practice for your whole team or organisation._

## 🎯 What You'll Do

You'll apply a lightweight governance model to your workflow, then share it with teammates using GitHub's native collaboration tools. By the end, your workflow will have clear ownership, documented scope, and safe defaults that let others run or extend it confidently.

## 📋 Before You Start

- Complete [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) so your workflow has cost guardrails in place before you share it.
- You need at least **Write** access to the repository (or Org Admin for some of the organisation-level settings below).

## Steps

### Review what you've built

Before sharing your workflow, ask:

- What does it do, and what access does it need?
- Who should be allowed to trigger it?
- What happens if the AI model returns an unexpected result?

Answering these questions in the workflow file itself is the first governance act.

### Add an owner comment to your frontmatter

Open your workflow file and add a `description` field so anyone reading it understands its purpose and owners.

```yaml
---
name: Daily Status Report
description: Summarises open issues and recent commits for the team stand-up. Owned by @your-team.
on:
  schedule: daily on weekdays
permissions:
  issues: read
  contents: read
timeout-minutes: 10
max-ai-credits: 1000
max-daily-ai-credits: 2500
---
```

The `description` appears in the GitHub Actions UI and in `gh aw list`, making the workflow discoverable.

> [!TIP]
> Keep `permissions:` narrow. Agentic workflows run with the token permissions you declare, so `read`-only is the right default for reporting workflows.

### Enable required reviewers for workflow changes (GitHub UI)

Protect the `.github/workflows/` directory so that changes require a pull request and an approved review.

1. In your repository, go to **Settings → Branches**.
2. Click **Add branch protection rule**.
3. Enter `main` as the branch name pattern.
4. Check **Require a pull request before merging** and set **Required approvals** to `1`.
5. Click **Save changes**.

Any change to a workflow file must now be reviewed before it can merge — the same protection that guards your application code now guards your AI workflows.

### Document the workflow

Add a `WORKFLOWS.md` file (or a section in your repository README) covering:

- **What it does** — one paragraph description.
- **Trigger** — when does it run?
- **Permissions** — what data can it access?
- **Ownership** — who maintains it and who to contact.
- **Cost** — approximate AIC per run (from Step 26).

> [!NOTE]
> On GHES or GHEC, your organisation may have an existing workflow catalogue. Ask your GitHub admin before creating a new documentation home.

### Share a link to the latest run

Once the workflow has run, share the **Actions** tab URL with your team:

```
https://github.com/<org>/<repo>/actions/workflows/daily-status.lock.yml
```

Teammates can check the latest report without needing CLI access.

### Enterprise: configure organisation-level defaults (GHEC / GHES)

If you manage an enterprise organisation, push governance settings to all repositories automatically.

1. Go to your **Organisation Settings → Actions → General**.
2. Under **Workflow permissions**, set the default token permission to **Read repository contents**.
3. Under **Allow GitHub Actions to create and approve pull requests**, uncheck the box for newly created workflows.
4. Consider enabling **Required Workflows** (GHEC/GHES 3.12+) to run your cost-reporting workflow across every repository in the org automatically.

> [!NOTE]
> Required Workflows are a GHEC and GHES 3.12+ feature. If the option is not visible, check your site admin console for the feature flag.

## ✅ Checkpoint

- [ ] You added a `description` field and scoped `permissions:` to your workflow frontmatter
- [ ] You set up a branch protection rule requiring at least one approved review before merging changes to `main`
- [ ] You documented the workflow's purpose, trigger, permissions, ownership, and cost in a Wiki page or `WORKFLOWS.md`
- [ ] You can share a direct link to the workflow's run history in the Actions tab
- [ ] (Enterprise) You reviewed your organisation's default Actions permissions and know how to enable Required Workflows

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

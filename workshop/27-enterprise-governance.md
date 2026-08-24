<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at Enterprise Scale

> _Moving from a single workflow to an organisation-wide deployment means thinking about policies, approvals, and rollout strategy — this step shows you how._

## 🎯 What You'll Do

You will configure org-level permissions for agentic workflows, add required reviewers to sensitive workflow files, and sketch a rollout plan. By the end, you'll have a clear, auditable governance model ready for your enterprise environment.

## 📋 Before You Start

- You have a working workflow from earlier in this workshop (for example, `daily-status.md`).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have organisation Owner or Admin access, or know who does (you can read along without it).

> [!NOTE]
> This step focuses on GitHub Enterprise Cloud (GHEC) and GitHub Enterprise Server (GHES 3.12+). Most concepts apply to team accounts on `github.com` too.

## Steps

### Understand the two governance layers

Agentic workflow governance spans two layers:

1. **Repository layer** — `CODEOWNERS`, branch protection rules, and required reviewers control who can merge changes to workflow files.
2. **Organisation layer** — Actions policies and Copilot seat assignments control who can run agentic workflows at all.

### Set CODEOWNERS for your workflow files

A `CODEOWNERS` file tells GitHub who must approve pull requests that touch specific paths. This protects your workflow files from unreviewed changes.

**GitHub UI path:**

1. In your repository on GitHub, click **Add file** → **Create new file** and name it `CODEOWNERS` (place it in `.github/`).
2. Add an entry that maps your workflow directory to the right team:

   ```
   .github/workflows/*.md  @my-org/platform-engineers
   .github/workflows/*.lock.yml  @my-org/platform-engineers
   ```

3. Click **Commit new file**.

> [!TIP]
> Use a dedicated `@my-org/agentic-workflow-reviewers` team rather than a personal username. Team membership is easier to manage as people join or leave.

### Enable required reviewers in branch protection

CODEOWNERS only takes effect when branch protection requires a review. In your repository, go to **Settings** → **Branches** → edit the `main` rule, then enable:

- **Require a pull request before merging**
- **Require review from Code Owners** (set to at least 1 approval)

Click **Save changes**. Now every workflow file change needs sign-off before it runs in production.

### Review organisation-level Actions policies

At the organisation level, you can restrict which workflows are allowed to run.

1. Go to your **organisation settings** → **Actions** → **General**.
2. Under **Policies**, choose the option that fits your risk posture: allow all actions, allow select actions (allowlist), or disable Actions entirely.
3. Under **Workflow permissions**, set the default token to **Read repository contents** and require explicit opt-in for write access.

> [!IMPORTANT]
> Agentic workflows that write issues or pull requests need `issues: write` or `contents: write` granted explicitly in the workflow frontmatter. Confirm your org policy permits this before enabling workflows in production.

### Draft a phased rollout plan

Roll out agentic workflows in phases:

| Phase | Scope | Gate |
|-------|-------|------|
| 1 — Pilot | 1–2 teams, 1–2 workflows | Manual review of every run for 2 weeks |
| 2 — Expansion | 5–10 teams | Audit reports clean, cost dashboard green |
| 3 — Self-service | All teams | Approved catalog, CODEOWNERS mandatory |

Review audit logs daily during Phase 1 and adjust prompts, permissions, and budgets before widening scope.

## ✅ Checkpoint

- [ ] Your `CODEOWNERS` file maps `.github/workflows/*.md` to a team or reviewer
- [ ] Your repository's branch protection rule requires review from Code Owners before merge
- [ ] You reviewed your organisation's Actions policies and confirmed the default token permission is set to read-only
- [ ] You drafted a phased rollout plan (even a rough one) that identifies your pilot teams and gates
- [ ] You understand why agentic workflows that write issues or PRs need explicit permission grants in frontmatter
- [ ] You know the command to install a workflow from your organisation's catalog

<!-- journey: all -->
Want to explore more governance topics? See [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md).
<!-- /journey -->

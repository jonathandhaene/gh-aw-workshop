<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organization

> _As agentic workflows multiply across teams, a little governance goes a long way — protecting your codebase, your budget, and your audit trail._

## 🎯 What You'll Do

You'll apply three lightweight governance controls to your practice repository: a required review policy for workflow changes, a branch protection rule that prevents unreviewed edits to compiled lock files, and a repository secret rotation reminder. By the end, you'll have a reusable checklist for rolling these controls out to an entire organization.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one agentic workflow committed in your practice repository.
- _(Enterprise users)_ Your GitHub administrator has enabled branch protection policies for your organization. If you're on GHES, confirm that branch rules are available in your instance settings.

## Steps

### Why governance matters for agentic workflows

An agentic workflow runs an AI model that can read repository content, call external tools, and write outputs. Three specific risks scale up as workflows multiply across teams:

- **Prompt drift**: someone edits the Markdown brief to change what the agent does, without anyone noticing.
- **Lock-file tampering**: the compiled `.lock.yml` is the executable artifact. A silent edit can change behavior without touching the `.md` source.
- **Credential sprawl**: secrets added for one workflow accumulate and go unrotated.

The controls below address each risk without adding bureaucratic overhead.

### Protect your workflow files with a branch rule

Lock files (`.lock.yml`) and workflow definitions (`.github/workflows/*.md`) are the most sensitive files in your repository. Require at least one review before they can be merged.

In your practice repository on GitHub:

1. Click **Settings** → **Branches**.
2. Click **Add branch ruleset**.
3. Set the target branch to `main`.
4. Enable **Require a pull request before merging** and set **Required approvals** to `1`.
5. Under **Restrict file paths**, add `.github/workflows/*.md` and `.github/workflows/*.lock.yml`.
6. Click **Create**.

> [!TIP]
> On GHES, **Restrict file paths** requires the `repository rulesets` feature flag. If it's not visible, ask your GitHub administrator to enable it, or use the classic branch protection path (Settings → Branches → Add rule → check **Require a pull request before merging**).

### Add a CODEOWNERS file for workflow files

A `CODEOWNERS` file automatically requests a review whenever the covered files change in a pull request.

Create `.github/CODEOWNERS`:

```text
# Require review for all agentic workflow files
.github/workflows/*.md @<your-github-username>
.github/workflows/*.lock.yml @<your-github-username>
```

Replace `@<your-github-username>` with your handle or a team handle (e.g., `@my-org/platform-team`).

> [!NOTE]
> CODEOWNERS reviews are only enforced if **Require review from code owners** is enabled in your branch rule. Return to **Settings** → **Branches** and tick that option after committing the file.

### Rotate secrets on a schedule

Secrets passed to agentic workflows — API keys, PATs, external service tokens — should be rotated regularly. Track them in a repository issue or `README` note:

```text
Secret: EXTERNAL_API_KEY
Rotated: 2025-01-15 — rotate again by 2025-07-15
Owner: @platform-team
```

A natural follow-on project: a scheduled workflow that reads a rotation-due date from a repository variable and posts a reminder issue when the date is within 14 days — combining [Connect a Live Data Source](16-connect-data-source.md) and [Persistent Memory](20-persistent-memory.md).

## ✅ Checkpoint

- [ ] You created a branch protection rule (or ruleset) requiring at least one PR review on your default branch
- [ ] You committed a `.github/CODEOWNERS` file that covers `*.md` and `*.lock.yml` in `.github/workflows/`
- [ ] You can explain what lock-file tampering is and why it matters for agentic workflows
- [ ] You identified at least one secret in your practice repository and documented its rotation schedule
- [ ] You can describe the three governance risks (prompt drift, lock-file tampering, credential sprawl) to a teammate

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

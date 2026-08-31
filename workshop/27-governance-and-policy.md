<!-- page-journey: all -->
# Govern and Roll Out Agentic Workflows at Scale

> _Deploying a single workflow is easy; deploying dozens across teams — consistently, safely, and with the right guardrails — requires a governance layer._

## 🎯 What You'll Do

You'll set up a lightweight governance model for agentic workflows in your organisation. By the end of this step, you'll have a repeatable rollout checklist, know how to enforce permissions and safe-output policies centrally, and understand the three controls that keep enterprise deployments predictable.

## 📋 Before You Start

- You completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to the target GitHub organisation or repository (or a sandbox where you can experiment).

## Steps

### Understand the three governance controls

Enterprise deployments of agentic workflows rely on three complementary controls:

| Control | What it does | Where it lives |
|---------|-------------|----------------|
| `permissions:` minimisation | Restricts what the agent can read or write | Workflow frontmatter |
| `safe-outputs:` allowlisting | Enumerates every GitHub side-effect the agent is allowed to create | Workflow frontmatter |
| Required PR reviews | Ensures a human approves workflow `.md` changes before they run in production | Branch protection rules |

All three must be in place before an agentic workflow is considered production-ready.

### Set branch protection on your workflow directory

Navigate to your repository on GitHub:

1. Go to **Settings → Branches → Add branch ruleset**.
2. Target the default branch (`main` or `master`).
3. Under **Require a pull request before merging**, enable **Require approvals** and set the count to at least **1**.
4. Enable **Restrict file paths** and add `.github/workflows/` as a protected path.
5. Click **Save changes**.

This ensures every change to a workflow file goes through a review before it can affect production runs.

### Centralise permitted safe outputs in a reusable template

If multiple teams create similar workflows, reduce drift by sharing a template that already includes the approved `safe-outputs:` block. Store it in `.github/workflow-templates/`:

```yaml
---
name: team-workflow-template
safe-outputs:
  create-issue-comment:
    limit: 1
  update-issue:
    limit: 5
permissions:
  contents: read
  issues: write
---
```

Teams import this template with `gh aw add` (covered in [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md)) and customise only the task brief — they cannot accidentally loosen the pre-approved safe-output policy without also editing the frontmatter, which requires a PR review.

> [!TIP]
> Pin a `max-ai-credits` value in the shared template so new workflows inherit a sensible cost ceiling by default. Teams can raise it with a reviewed PR if they need more headroom.

### Review the governance guide

The full policy reference — including GHES-specific controls, organisation-level Copilot policy settings, and runner label governance — is at [`guides/governance`](https://github.github.com/gh-aw/guides/governance/).

Read the **Rollout checklist** section before you enable any agentic workflow for a production schedule.

## ✅ Checkpoint

- [ ] You can name the three governance controls and explain what each one protects against
- [ ] Your repository (or a sandbox repository) has a branch ruleset that requires PR approval for changes to `.github/workflows/`
- [ ] You created or reviewed a shared workflow template with a pre-approved `safe-outputs:` block
- [ ] You read the Rollout checklist in the governance guide and confirmed your workflow meets every item
- [ ] You can explain to a teammate why `safe-outputs:` allowlisting is required even when `permissions:` is already minimal
- [ ] You know where to find GHES-specific Copilot policy settings in your admin console

**Next:** [Welcome Back — You've Completed the Workshop](14-next-steps.md)

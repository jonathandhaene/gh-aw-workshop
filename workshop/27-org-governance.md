<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Individual workflows are powerful — but at scale, policy is what keeps them safe, consistent, and accountable._

## 🎯 What You'll Do

You will apply organisation-level controls that govern every agentic workflow running under your org. By the end, you will have checked the Copilot policy, reviewed the org-wide AIC cap approach, and confirmed your workflows appear in the audit trail.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to a GitHub organisation (GHEC or GHES) — or you are reading this to understand what your org admin needs to configure before production use.
- `gh aw` is installed and authenticated with `read:org` scope (see [Install the gh-aw CLI Extension](06-install-gh-aw.md)).

> [!NOTE]
> This step is aimed at enterprise teams. If you are on a personal plan, read through and return later when you move to an enterprise environment.

## Steps

### Understand the three governance layers

Agentic workflows sit inside a permission stack with three layers, each narrower than the one above.

| Layer | Who controls it | What it governs |
|---|---|---|
| Organisation policy | Org admin | Which repos can run agentic workflows, which models are allowed, the org-wide AIC cap |
| Repository settings | Repo admin | Per-repo AIC limits, required-approval gates |
| Workflow frontmatter | Workflow author | Per-run `max-ai-credits`, `permissions:`, `safe-outputs`, `network.allowed-domains` |

Your workflow-author responsibilities cover the bottom layer. This step focuses on the top two.

### Check the org-level Copilot policy

1. Open your org's **Settings** → **Copilot** → **Policies**.
2. Confirm **GitHub Actions can use Copilot** is set to **Enabled**.
3. If **Allowed models** is shown, confirm your workflow's `engine:` matches an entry on that list.

> [!TIP]
> No Copilot policy page? Your org may not have Copilot Enterprise enabled. Ask your admin to check the billing tier.

### Set an org-wide AI Credit budget

Per-workflow caps limit individual runs; the org-wide cap is the ceiling across all repositories.

Your org admin sets this in **Settings** → **Billing** → **AI Usage** → **Spending limit**. To suggest a starting value:

1. Find the P90 cost per run from `gh aw forecast` (see [Project Future AI Credit Costs](side-quest-26-01-forecast-costs.md)).
2. Multiply by the number of workflows in the org and expected monthly runs.
3. Add 30 % headroom.

Bring that number to your admin with a written justification.

### Enable required approval for write-capable workflows

For workflows that write to issues, PRs, or file contents:

1. In your repository, open **Settings** → **Actions** → **General**.
2. Under **Required approvals**, enable **Require approval for first-time contributors** (or stricter if public-facing).

When approval is required, a run pauses at the agent step with a **Review pending** badge. An approver clicks **Review deployments** → **Approve and deploy** to continue.

### Confirm your workflows appear in the org audit log

1. Open your org's **Settings** → **Audit log**.
2. Search for `action:copilot.agentic_workflow_run`.
3. Confirm your recent runs appear with the correct **repository** and **actor** values.

> [!TIP]
> For a deeper audit breakdown, see [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).

### Use a template workflow repository (recommended)

Create a `.github` repository in your org. Add approved templates in `.github/workflow-templates/`. Templates can pre-populate `permissions:`, `max-ai-credits`, `timeout-minutes`, and an approved `engine:` so new workflows start with guardrails already in place.

## ✅ Checkpoint

- [ ] You confirmed the org's Copilot policy allows GitHub Actions to use Copilot
- [ ] You identified or drafted a recommendation for an org-wide AIC spending limit
- [ ] You found the required-approval setting in repository or org settings
- [ ] You located at least one recent agentic workflow run in the organisation audit log
- [ ] You know what belongs in an approved workflow template

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->


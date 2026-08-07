<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Individual workflow guardrails keep one run safe — organisation-level policy keeps every run safe, at scale._

## 🎯 What You'll Do

You'll configure the org-level controls that govern agentic workflows across all your repositories: restrict which repos may run them, cap AI Credit spend at the org level, and require human approval before workflows write to privileged resources.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have **owner** or **enterprise admin** access to your GitHub organisation.
- Your environment is GHEC or GHES 3.12+. GHES-only or GHEC-only constraints are noted inline.

## Steps

### Understand the three layers of governance

Governance works at three levels:

| Layer | Who controls it | What it covers |
|---|---|---|
| **Enterprise policy** | Enterprise admin | Which orgs may use Copilot-powered workflows |
| **Organisation policy** | Org owner | Which repos may run them; org-wide AI Credit cap |
| **Workflow** | Author | Per-workflow `max-ai-credits`, `permissions:`, `safe-outputs` |

This step focuses on the **organisation layer**.

### Set the Copilot policy for your organisation

Before any repository in your org can run an agentic workflow, Copilot must be enabled and **Copilot in GitHub Actions** must be allowed.

1. Navigate to your organisation's **Settings** → **Copilot** → **Policies**.
2. Under **Copilot in GitHub Actions**, choose:
   - **Enabled for all repositories** — any repo in the org can run agentic workflows.
   - **Enabled for selected repositories** — opens a picker so you can allowlist specific repos.
   - **Disabled** — no repo can run agentic workflows (useful during evaluation).
3. Click **Save**.

> [!TIP]
> Start with **selected repositories** during a pilot and widen the allowlist once cost and safety outcomes look good.

### Set an organisation-wide AI Credit cap

An org-level cap sets a ceiling across all repositories. It acts as a safety net even if individual workflows omit their own `max-ai-credits` limit.

1. Go to **Settings** → **Copilot** → **Usage and billing** → **AI Credit limits**.
2. Set a **Monthly organisation cap**. A reasonable starting value for 10–20 active workflow authors is **50 000 AIC per month**.
3. Optionally set a **Daily organisation cap** to prevent a runaway scheduled workflow from exhausting the monthly budget.
4. Click **Save limits**.

> [!NOTE]
> Org caps complement per-workflow frontmatter values — a run is capped by whichever limit is reached first.

### Require environment protection for privileged writes

Workflows that write to issues, pull requests, or branches should run inside a GitHub **environment** with a required reviewer. This creates a human-approval gate before any agentic write executes.

1. In your repository, go to **Settings** → **Environments** → **New environment**.
2. Name it `production-writes` (or follow your team's naming convention).
3. Enable **Required reviewers** and add the people or teams who must approve runs.
4. Add `environment: production-writes` to your workflow frontmatter, for example:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
environment: production-writes
permissions:
  issues: write
  contents: read
---
```

Recompile after saving the file:

```bash
gh aw compile
```

On the next run GitHub pauses the job and notifies reviewers before any `safe-outputs` write action fires.

<details>
<summary>🖥️ GitHub UI alternative for the compile step</summary>

Commit the updated `daily-status.md` through the GitHub editor (pencil icon ✏️), then let your CI compile job regenerate the lock file. The environment gate takes effect as soon as the new `.lock.yml` is present.

</details>

## ✅ Checkpoint

- [ ] You set the **Copilot in GitHub Actions** policy to a specific allowlist of repositories (or org-wide) in your organisation settings
- [ ] You configured a monthly AI Credit cap at the organisation level
- [ ] You created a `production-writes` environment with at least one required reviewer in your practice repository
- [ ] You added `environment: production-writes` to your workflow frontmatter and recompiled it
- [ ] You can explain the three layers of governance (enterprise, org, and per-workflow)
- [ ] You located your organisation's per-repository AI Credit usage in the billing dashboard

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

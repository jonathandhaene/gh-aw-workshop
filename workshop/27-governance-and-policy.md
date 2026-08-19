<!-- page-journey: all -->
<!-- page-adventure: enterprise -->
# Governance and Policy Workflows

> _Turn your AI Credit controls and audit trail into an automated governance layer that keeps your organisation's agentic workflows compliant and visible — without manual review cycles._

## 🎯 What You'll Do

You'll create a scheduled agentic workflow that acts as a lightweight governance agent: it scans your repository's active agentic workflows for missing or incomplete policy fields, then files a GitHub issue summarising any gaps. By the end, you'll have a reusable compliance check running automatically each week.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and understand `max-ai-credits`, `permissions:`, and `timeout-minutes` frontmatter fields.
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and can read `gh aw audit` output.
- You are working in a GitHub Enterprise Cloud or GitHub Enterprise Server 3.12+ environment, or a standard `github.com` repository. The patterns in this step apply everywhere, but the compliance story is most important in enterprise settings.

## Understand governance as a workflow problem

Governance means making sure policy fields (`permissions:`, AI Credit caps, timeouts) are consistently present and correct across all your workflows. The insight is that an agentic workflow is the right tool to enforce its own policies.

## Define your governance policy

Decide which frontmatter fields are required in every production workflow. A common enterprise baseline: `permissions:`, `max-ai-credits:`, `max-daily-ai-credits:`, and `timeout-minutes:`. When any of these are missing, the risk of runaway spend or over-privileged tokens increases.

## Write the governance workflow

Create `.github/workflows/governance-check.md` using the GitHub UI. Navigate to **Add file** → **Create new file**, set the path, then paste the following content (frontmatter then agent brief):

```yaml
---
name: Governance Policy Check
on:
  schedule:
    - cron: "0 9 * * 1"   # Every Monday at 09:00 UTC
  workflow_dispatch:

permissions:
  contents: read
  issues: write

max-ai-credits: 150
max-daily-ai-credits: 150
timeout-minutes: 20

safe-outputs:
  create-issue:
    limit: 1
---
```

After the closing `---`, add the agent brief:

````markdown
You are a governance auditor for agentic workflows in this repository.

## Task

1. List every `.md` file inside `.github/workflows/`. Ignore `*.lock.yml` files and any file that does not contain a frontmatter block (opening and closing `---`).
2. For each workflow file, extract the frontmatter and check whether the following fields are present and non-empty:
   - `permissions:`
   - `max-ai-credits:`
   - `max-daily-ai-credits:`
   - `timeout-minutes:`
3. Build a Markdown table with one row per workflow. Columns: **File**, **permissions**, **max-ai-credits**, **max-daily-ai-credits**, **timeout-minutes**. Mark each cell ✅ if present and ❌ if missing or empty.
4. If every workflow passes every check, do not create an issue. Instead, output a brief "All workflows are compliant" message in the run log and finish.
5. If one or more workflows fail any check, create a GitHub issue titled "⚠️ Governance check: policy fields missing" with the compliance table in the body and a short remediation guide listing the failing files and the specific fields each one is missing.
````

When you're done, scroll down and click **Commit new file**.

> [!TIP]
> The `workflow_dispatch:` trigger lets you run the governance check on demand — useful when you've just onboarded a new workflow and want to verify it passes before the next scheduled run.

## Trigger a test run

After committing:

1. Go to the **Actions** tab in your repository.
2. Select **Governance Policy Check** in the left sidebar.
3. Click **Run workflow** → **Run workflow** to trigger it manually.
4. Watch the run. If any of your existing workflows are missing policy fields, the agent creates a compliance issue for you.

<details>
<summary>🖥️ Verifying the run from the terminal</summary>

```bash
gh run list --workflow governance-check.lock.yml --limit 5
```

Then view the most recent run:

```bash
gh run view <RUN_ID> --log
```

</details>

## Extend to organisation-wide governance

Once this works in one repository, promote it to a reusable workflow using the `gh aw add` pattern from [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md). Centralising governance means policy updates propagate to every consuming repository at the next scheduled run.

## ✅ Checkpoint

- [ ] I created `.github/workflows/governance-check.md` with the correct frontmatter fields
- [ ] I triggered a manual run from the **Actions** tab and it completed successfully
- [ ] I can explain why `safe-outputs: create-issue: limit: 1` prevents duplicate issues
- [ ] I know which policy fields my organisation requires in every production workflow
- [ ] If my repository had non-compliant workflows, I can see the filed issue with a compliance table

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose your next path.
<!-- /journey -->

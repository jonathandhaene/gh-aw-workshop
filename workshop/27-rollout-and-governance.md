<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Roll Out Agentic Workflows Across Your Organisation

> _Shipping one workflow proves the technology works — governing how your whole team uses it is what makes it sustainable._

## 🎯 What You'll Do

Apply a lightweight governance framework to your agentic workflows so you can safely onboard teammates and expand usage organisation-wide. By the end of this step you'll have a reusable policy template, a permissions baseline, and a cost guard that protects shared budgets when multiple teams run workflows in parallel.

## 📋 Before You Start

- You have a working scheduled workflow from [Build: Daily Repo Status Workflow](07-your-first-workflow.md).
- You have reviewed AI Credit budgets from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have access to an organisation on GitHub (github.com, GHEC, or GHES 3.12+).

> [!NOTE]
> If you are working on a personal repository, you can still follow this step — treat yourself as the "team lead" and skip the org-level admin steps.

## Steps

### Understand the two layers of governance

Agentic workflow governance has two layers:

1. **Repository-level**: permissions, safe-output declarations, and spending caps inside each workflow file.
2. **Organisation-level**: which repositories can use Copilot, which models are allowed, and who can approve new workflows.

Both layers matter. Repository-level controls protect individual workflows from abuse. Organisation-level controls prevent one team's experimental workflow from consuming shared Copilot credits before anyone notices.

### Set a repository-level permissions baseline

Every production agentic workflow should declare the minimum permissions it needs. Open your workflow `.md` file and confirm the frontmatter follows the least-privilege pattern:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: read
  pull-requests: read
max-ai-credits: 800
max-daily-ai-credits: 2000
---
```

If your workflow writes output (comments, labels, issues), add only the specific write permission it needs — for example `issues: write` — and nothing broader.

<details>
<summary>🖥️ Edit the frontmatter in the GitHub UI</summary>

1. Open your repository on GitHub.
2. Navigate to `.github/workflows/daily-status.md` (or whichever file contains your workflow).
3. Click the **pencil icon (✏️)** to open the editor.
4. Update the `permissions:` block and `max-ai-credits:` fields.
5. Click **Commit changes** and commit directly to your branch.

</details>

### Create a governance README for your team

Add a short policy document so teammates know how to propose, review, and ship new workflows.

Create `.github/agentic-workflows-policy.md` in your repository (or organisation's `.github` repository to make it org-wide):

```markdown
# Agentic Workflow Policy

## Proposing a new workflow

1. Open an issue describing the workflow's purpose, trigger, and expected outputs.
2. Get approval from a team lead before creating the workflow file.
3. Start with `max-ai-credits: 500` and raise the cap only after reviewing real usage data.

## Required frontmatter for all production workflows

- `permissions:` — minimum required; no `write-all` or `admin` scopes.
- `max-ai-credits:` — always set; no unlimited workflows in production.
- `max-daily-ai-credits:` — set to 3× the per-run cap as a starting point.

## Review cadence

Review AIC usage monthly using `gh aw audit` and adjust caps accordingly.
```

<details>
<summary>🖥️ Create this file using the GitHub UI</summary>

1. In your repository, click **Add file** → **Create new file**.
2. Enter `.github/agentic-workflows-policy.md` as the file name.
3. Paste the policy content above.
4. Click **Commit new file**.

</details>

### Enable Copilot for the right repositories (org admins)

If you manage an organisation, control which repositories can run agentic workflows through the Copilot policy page:

1. Go to your organisation on GitHub and click **Settings**.
2. Under **Copilot**, select **Policies**.
3. Set **GitHub Copilot in GitHub Actions** to **Enabled for selected repositories** rather than **All repositories**.
4. Add only the repositories whose workflows have been reviewed and approved.

> [!NOTE]
> On GHEC and GHES, this setting is enforced at the organisation level regardless of individual repository settings. On github.com personal accounts, Copilot availability is controlled by your subscription.

### Set an organisation-level daily spending cap

Protect shared Copilot credits from runaway workflows by configuring a daily budget at the organisation level:

1. Go to **Settings** → **Billing and plans** → **Copilot**.
2. Set a **Monthly spending limit** that covers expected usage for all workflows, with a 20 % buffer for spikes.
3. Enable spending alerts at 80 % of that limit so you get an email before the cap is reached.

This cap is a safety net, not a replacement for per-workflow `max-ai-credits:` limits. Both layers should be in place.

### Compile and push your updated workflow

If you changed the workflow frontmatter, regenerate the lock file:

```bash
gh aw compile
```

Then commit and push both files:

```bash
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "chore: apply governance baseline to daily-status workflow"
git push
```

## ✅ Checkpoint

- [ ] Your workflow frontmatter has an explicit `permissions:` block with only the scopes it needs
- [ ] Your workflow frontmatter has both `max-ai-credits:` and `max-daily-ai-credits:` set
- [ ] You have created or planned a policy document explaining how your team proposes and reviews new workflows
- [ ] You know where to find the Copilot policy settings for your organisation (or personal account)
- [ ] You can explain the difference between repository-level and organisation-level governance controls
- [ ] `gh aw compile` completed without errors and the `.lock.yml` file is committed

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

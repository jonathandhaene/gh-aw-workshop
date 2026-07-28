<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Enterprise teams need more than working workflows — they need workflows that stay within policy as your organisation grows._

## 🎯 What You'll Do

You'll apply a governance layer to your agentic workflow environment. By the end of this step you'll have a documented policy for which workflows run, who can approve changes, and how to detect drift before it reaches production.

## 📋 Before You Start

- You have a deployed workflow with cost controls from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You can edit files in your repository's `.github/workflows/` directory.
- You are working in a GitHub organisation (github.com, GHEC, or GHES 3.12+).

## Steps

### Understand what "governance" means for agentic workflows

A classic GitHub Actions workflow is a deterministic script: you review it once and know what it does. An agentic workflow is different — the AI model interprets your instructions at runtime, so the final action depends on both the workflow file **and** the model's current behaviour.

Governance bridges this gap. It answers three questions:

1. **Who** is allowed to create or modify a workflow that can write to your repository?
2. **What** is each workflow allowed to read, write, or call?
3. **When** should a workflow be reviewed, retired, or blocked?

### Add a CODEOWNERS rule for workflow files

The simplest governance control is a mandatory human review on every workflow change. Add a rule to `.github/CODEOWNERS` so that any pull request touching `.github/workflows/` requires approval from a designated team.

Open `.github/CODEOWNERS` (create it if it doesn't exist) and add:

```text
# Require workflow-team review for all agentic workflow changes
.github/workflows/ @your-org/workflow-team
```

Replace `your-org/workflow-team` with the actual GitHub team slug for your organisation's workflow approvers. From now on, a pull request that changes any file under `.github/workflows/` cannot be merged without a review from a member of that team.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your repository on GitHub.
2. Click the **Code** tab, then open the `.github/` directory.
3. If `CODEOWNERS` doesn't exist, click **Add file → Create new file** and name it `CODEOWNERS`.
4. If it does exist, click the file and then the pencil icon (✏️).
5. Add the rule above, then click **Commit changes**.

</details>

### Define a minimal-permission baseline

Every agentic workflow should declare only the permissions it actually uses. Open any workflow frontmatter and audit the `permissions:` block:

```yaml
permissions:
  contents: read   # only if reading repo files
  issues: write    # only if posting issue comments
  pull-requests: read   # only if reading PR data
```

Remove any permission that the workflow's Markdown body doesn't explicitly need. Fewer permissions mean a smaller blast radius if the agent is manipulated or makes a mistake.

> [!TIP]
> Review permissions each time you add a new data source or safe-output target. Permission creep is the most common governance finding in agentic workflow audits.

### Document each workflow's purpose and owner

Create a short registry file at `.github/workflows/REGISTRY.md`. Add one row per agentic workflow:

```markdown
| Workflow file | Purpose | Owner team | Review cadence | Status |
|---|---|---|---|---|
| daily-status.md | Daily repo health summary | @your-org/workflow-team | Quarterly | Active |
| pr-reviewer.md | Auto-review open pull requests | @your-org/workflow-team | Quarterly | Active |
```

This file becomes the audit trail for compliance reviews. Update it whenever a workflow is added, modified, or retired.

### Set up a review cadence

Even well-designed workflows drift over time — AI model behaviour changes, repository structure evolves, and requirements shift. Schedule a quarterly review in your team's project tracker. During each review:

- Re-read the Markdown body and confirm the instructions still match the intended behaviour.
- Check the latest run logs in the **Actions** tab for unexpected outputs.
- Verify that the permissions block still matches what the workflow actually does.
- Confirm the workflow still has an active owner on the `CODEOWNERS` team.

> [!NOTE]
> The [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/) covers organisation-wide policy controls, including enterprise-managed Copilot policies and runner access restrictions for GHES environments.

## ✅ Checkpoint

- [ ] `.github/CODEOWNERS` includes a rule requiring review for all files under `.github/workflows/`
- [ ] At least one workflow's `permissions:` block has been audited and scoped to minimum required access
- [ ] `.github/workflows/REGISTRY.md` exists with a row for each active agentic workflow
- [ ] You can name the three governance questions (who, what, when) in your own words
- [ ] You know the URL of the gh-aw governance guide for further reading

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

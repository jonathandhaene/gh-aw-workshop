# Govern Agentic Workflows Across Your Organisation

> _Enterprise teams need more than individual cost limits — they need org-wide policy controls that keep every agentic workflow safe and compliant._

## 🎯 What You'll Do

You'll configure organisation-level governance settings for agentic workflows: restricting which repositories can run them, setting default model and token limits, and enabling an approval gate for new workflows before they can execute. By the end, your organisation will have a baseline policy that applies automatically to every team.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are an **organisation owner** on GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) 3.14 or later.
- You have at least one agentic workflow running in a repository within your organisation.

## Steps

### Understand the governance layer

Individual workflows set their own `max-ai-credits` and `permissions`. Governance adds a **floor and ceiling** at the organisation level: a default that applies when a workflow does not specify a limit, and a hard cap that cannot be overridden by individual workflow authors.

The controls live in your organisation's **Copilot** settings — not in any individual repository.

### Open organisation Copilot settings

1. Navigate to your organisation on GitHub (for example, `github.com/your-org`).
2. Click **Settings** in the top navigation bar.
3. In the left sidebar, click **Copilot**, then select **Policies**.

![Organisation Copilot Policies page](images/27-org-copilot-policies.png)

> [!NOTE]
> On GitHub Enterprise Server, the path is **Admin console → Copilot → Policies**. The options described below are the same on both platforms.

### Set a default AI credit limit per workflow run

Under **Agentic Workflows**, find **Default AI credit limit per run**. This value applies to every workflow that does not declare its own `max-ai-credits` in frontmatter.

Set a conservative default — for example, **500 AIC** — so that new workflows that forget to include a limit do not consume unbounded credits:

```yaml
# Example: what a per-workflow frontmatter override looks like
max-ai-credits: 2000          # overrides the org default for this workflow only
max-daily-ai-credits: 5000
```

The org-level default silently protects workflows where no override is set.

### Set a hard cap per workflow run

Under **Maximum AI credit limit per run**, enter the ceiling your organisation wants to enforce. No individual workflow can exceed this value, even if its frontmatter specifies a higher number.

A common starting point for enterprise teams is **5 000 AIC** per run.

### Restrict which repositories can run agentic workflows

Under **Repository access**, choose one of:

| Option | When to use |
|--------|-------------|
| **All repositories** | Greenfield org where all teams are trusted |
| **Selected repositories** | Controlled rollout — allowlist specific repos |
| **Disabled** | Temporarily block all agentic workflow execution org-wide |

For a first enterprise deployment, **Selected repositories** is the safest starting point. Add each repository that has been reviewed and approved by your platform team.

### Enable the workflow approval gate

The approval gate requires a designated reviewer to approve a new agentic workflow before its first run. This prevents unreviewed AI prompts from executing against production data.

1. Under **Workflow approval**, toggle **Require approval for new workflows** to **Enabled**.
2. In **Approver teams**, add the GitHub team responsible for reviewing agentic workflow PRs (for example, `@your-org/platform-engineering`).

Once enabled, any newly created `*.md` agentic workflow file triggers an approval request. Existing workflows are not affected.

> [!TIP]
> Pair this gate with a pull request CODEOWNERS rule on `.github/workflows/*.md` so the same platform team is automatically requested as a PR reviewer whenever a workflow file is changed.

### Verify your policy is active

After saving, open a workflow run in one of your selected repositories:

1. Go to the repository → **Actions** tab.
2. Click a completed agentic workflow run.
3. In the run summary, find the **Governance** section — it lists the effective AIC limit and whether an approval was required.

If the governance section shows your new org-level cap rather than the per-workflow value, the policy is active.

## ✅ Checkpoint

- [ ] You located the Copilot Policies page in your organisation settings
- [ ] You set a default AI credit limit per run at the organisation level
- [ ] You configured a hard maximum AI credit cap that individual workflows cannot override
- [ ] You restricted agentic workflow execution to selected repositories
- [ ] You enabled the workflow approval gate and assigned an approver team
- [ ] You verified that a workflow run's governance section reflects the new org-level policy

<!-- journey: enterprise -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

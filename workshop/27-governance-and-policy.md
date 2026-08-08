<!-- page-journey: all -->
# Govern Agentic Workflows Across Your Organisation

> _Organisations that run many agentic workflows need consistent guardrails — learn how to set policies that keep every workflow predictable and auditable._

## 🎯 What You'll Do

Set organisation-level controls for agentic workflows: restrict which AI models can be used, cap AI Credit spending, and configure approval steps before a workflow writes to repositories. By the end you'll know the governance knobs that platform engineers and security teams care about most.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have access to your GitHub organisation's **Settings** page (owner or admin role required). If you are a learner without org admin rights, follow the read-only path highlighted in each section.

## Steps

### Understand the two layers of governance

Governance operates at two layers:

1. **Organisation policy** — what any workflow in the org is allowed to do.
2. **Workflow-level controls** — per-workflow budget and permission settings in the `.md` frontmatter.

Both layers must agree. If org policy blocks a model, a workflow that requests it fails even if the frontmatter looks correct.

### Set an allowed-models policy

You can restrict which AI models agentic workflows may call inside your organisation.

1. Go to your organisation on GitHub and click **Settings**.
2. In the left sidebar, under **Copilot**, click **Policies**.
3. Locate the **Agentic workflow models** section.
4. Select **Allow specific models** and check the models your organisation approves.
5. Click **Save**.

> [!NOTE]
> Workflows that request a model outside the approved list will fail at the model-selection step and log a clear error. Review `gh aw logs` to confirm the model name in the error message matches what you configured.

### Require workflow approval for write actions

For regulated environments, you can require a human to approve a workflow run before it executes write steps (create issues, open pull requests, push commits).

1. In **Settings → Actions → General**, scroll to **Workflow approvals**.
2. Enable **Require approval for workflows from outside collaborators** or the equivalent policy for your deployment.
3. For GHES environments, ask your admin to confirm whether the site-level `require_workflow_approval` setting is active.

> [!NOTE]
> <details>
> <summary><b>Enterprise Server users: additional admin steps may apply.</b></summary>
>
> On GHES, workflow approval policies are configurable at the site level by a site administrator in addition to org-level settings. If you enable org-level approval but runs bypass it, check the GHES site admin panel under **Actions → Policies**.
>
> </details>

### Set an org-wide AIC spending limit

In addition to per-workflow `max-ai-credits` (covered in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)), you can set a hard ceiling for the whole org.

1. Go to **Settings → Billing and licensing → Spending limits**.
2. Locate the **Copilot (AI workflows)** section.
3. Enter a monthly AIC ceiling for the organisation.
4. Click **Save spending limit**.

Once the ceiling is reached, all agentic workflow runs in the org are blocked until the next billing cycle — plan your limit with room to spare.

## ✅ Checkpoint

- [ ] You can describe the difference between org-level policy and per-workflow frontmatter controls
- [ ] You reviewed (or were shown) your organisation's **Agentic workflow models** policy page
- [ ] You know which models are approved in your organisation (or confirmed that all models are allowed)
- [ ] You identified whether workflow approvals are required before write actions in your org
- [ ] You located the org-level AIC spending limit for Copilot workflows
- [ ] You know where to find the policy document for your org (or created a placeholder `AGENTIC_WORKFLOW_POLICY.md`)

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

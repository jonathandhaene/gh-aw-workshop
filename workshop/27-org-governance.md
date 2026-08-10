<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _One workflow is a productivity win; a hundred unmanaged workflows is an operational risk — governance lets your team scale agentic automation confidently._

## 🎯 What You'll Do

You'll learn how to manage agentic workflows at the organisation level: centralising reusable workflow templates, applying org-wide permission policies, and understanding the audit trail that helps enterprise teams stay compliant. By the end, you'll have a plan for rolling out agentic workflows to your team without losing visibility or control.

## 📋 Before You Start

- Complete [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or be comfortable with `max-ai-credits` and `timeout-minutes` settings.
- Have owner or admin access to a GitHub organisation (or use a personal account that acts as your test org).

## Governance overview

When agentic workflows move from personal experiments to team tooling, three questions arise:

1. **Who controls what the agent can do?** — Permissions and safe-outputs scope the blast radius of each workflow.
2. **How does the team share and update approved workflows?** — A reusable workflow catalog avoids drift across repositories.
3. **What happened, and when?** — Audit artifacts and run logs provide an evidence trail for compliance reviews.

The sections below cover each in turn.

### Scope permissions at the org level

Each agentic workflow declares its own [`permissions:`](https://github.github.com/gh-aw/reference/permissions/) block. At the organisation level, you can enforce a ceiling on those permissions in **Settings → Actions → General → Workflow permissions**.

Set the default to **Read repository contents and packages permissions**. Individual workflows can then request the minimum they need — but they cannot exceed what the org allows.

> [!TIP]
> On GitHub Enterprise Server and GitHub Enterprise Cloud, repo admins can also restrict which workflows are allowed to run and whether they can call reusable workflows from other repos. Find these controls under **Settings → Actions → General**.

### Build a reusable workflow catalog

In [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md) you published a single workflow template. For an org-wide catalog:

1. Create a dedicated repository (for example, `your-org/agentic-workflow-catalog`) and set its visibility to **Internal** so all org members can read it.
2. Push your approved workflow `.md` files and their compiled `.lock.yml` files there.
3. Add a `README.md` that lists each workflow, its trigger, required secrets, and the `gh aw add` command to install it.

Team members install a workflow with one command:

```bash
gh aw add your-org/agentic-workflow-catalog/daily-status
```

When you release an updated version, members can pull the update with the same command.

<details>
<summary>🖥️ GitHub UI alternative — browsing the catalog</summary>

1. Navigate to `github.com/your-org/agentic-workflow-catalog`.
2. Open the workflow `.md` file you want to install.
3. Copy the raw URL of its `.lock.yml` counterpart.
4. In your target repository, go to **Add file → Create new file**, name it `.github/workflows/<name>.lock.yml`, and paste the content.

Note: the `gh aw add` command handles this automatically and is strongly preferred for accuracy.

</details>

### Apply org-wide cost policies

You set per-workflow budgets in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md). At org scale, coordinate a shared policy:

- Agree on a standard `max-ai-credits` ceiling for low-stakes scheduled workflows (e.g. 500 AIC) and event-driven workflows (e.g. 200 AIC).
- Document the policy in the catalog `README.md` so authors know what to put in their frontmatter.
- Use the [billing dashboard](https://github.com/settings/billing) org view to watch aggregate AIC spend across all workflows.

> [!NOTE]
> On GitHub Enterprise Cloud, billing administrators can view AIC usage per repository. This makes it straightforward to identify which team is driving high consumption before it becomes a problem.

### Review the audit trail at org scale

[Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) covered per-run auditing. For ongoing org-wide oversight:

- Pipe `gh aw audit` output from your highest-volume workflows into a shared tracking issue or wiki page on a weekly schedule.
- Use the GitHub audit log (`Settings → Audit log`) to see when workflow files were created, modified, or deleted org-wide.
- For regulated environments (SOC 2, ISO 27001), archive `gh aw audit` artifacts to external storage before the 90-day GitHub retention window closes.

### Communicate change to your team

Before enabling new agentic workflows in shared repositories:

1. Document the workflow's trigger, permissions, safe-outputs, and cost ceiling in its frontmatter comments.
2. Open a pull request so teammates can review the workflow brief before it goes live.
3. Do a dry run with a `workflow_dispatch` trigger before switching to an automated trigger.

## ✅ Checkpoint

- [ ] You set the default workflow permission in your org or repository to **Read repository contents**
- [ ] You identified or created a repository to host your org's agentic workflow catalog
- [ ] You documented at least one workflow in the catalog with its `gh aw add` command
- [ ] You agreed on (or documented) an org-wide `max-ai-credits` policy
- [ ] You know where to find aggregate AIC usage for your organisation
- [ ] You can name two audit artifacts that support a compliance review
- [ ] You have a process for communicating workflow changes to your team

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

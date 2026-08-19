<!-- page-journey: all -->
<!-- page-adventure: main -->
# Govern Agentic Workflows Across Your Organisation

> _Managing one workflow is easy — managing dozens across multiple teams requires a plan. This step shows you how to enforce consistent standards, permissions, and budgets at the organisation level._

## 🎯 What You'll Do

You'll explore organisation-level controls that keep agentic workflows safe and predictable at scale: required workflows, permission boundaries, centralised workflow libraries, and org-wide spending policies. By the end, you'll know which levers to pull when rolling out agentic workflows to a whole team or enterprise.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a workflow that runs reliably on your own repository.
- You have (or can imagine) an organisation with multiple repositories and teams.

> [!NOTE]
> You don't need admin access to learn this material. The configuration examples are illustrative — read through them and note what you'd ask your administrator to set up.

## Set permission boundaries

Every agentic workflow inherits the repository's default token permissions. Tighten that default at the organisation level so no workflow accidentally receives write access unless it explicitly declares it.

In your organisation settings, navigate to **Actions → General → Workflow permissions** and set the default to **Read repository contents and packages**.

At the workflow level, frontmatter already follows this pattern:

```yaml
---
permissions:
  contents: write
  pull-requests: write
---
```

When the organisation default is `read`, any workflow that does _not_ declare permissions runs with minimal access — a safe default that is easy to audit.

## Create an organisation-wide workflow library

In [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md) you published a single workflow template. At scale, create one shared repository — conventionally named `.github` or `workflow-templates` — to host approved templates that any team can adopt:

```
your-org/
  .github/
    workflows/
      daily-status.md        ← approved template
      pr-reviewer.md         ← approved template
```

Teams install from this library with:

```bash
gh aw add your-org/.github/workflows/daily-status.md
```

When the platform team updates a template, teams pick up the latest version by running `gh aw add` again.

## Use required workflows to enforce standards

GitHub organisations can designate specific workflows as _required_ — they run on every repository that matches a policy target, regardless of what the repository owner does. This is the most powerful governance tool available.

Required workflows are configured in **Organisation settings → Actions → Required workflows**.

Good candidates for required agentic workflows:

| Workflow | What it enforces |
|----------|-----------------|
| `compliance-check.md` | Verifies pull requests meet a policy checklist before merge |
| `dependency-audit.md` | Scans new dependencies on push |
| `cost-report.md` | Generates a weekly AIC usage summary for platform teams |

> [!TIP]
> Start with one required workflow and validate its behaviour before adding more. Required workflows that post noisy output will erode team trust quickly.

## Set organisation-level spending limits

Individual `max-ai-credits` frontmatter limits protect individual workflows. Organisation-level limits protect the entire billing account.

In the GitHub billing dashboard, go to **Billing and plans → Spending limits → Copilot** to set a monthly cap. Add a budget alert at 80% of the limit so you're notified before the cap is hit.

For GHEC and GHES environments, spending policies may be configured at the enterprise level. Ask your GitHub administrator which layer takes precedence.

## ✅ Checkpoint

- [ ] You can explain the three main governance levers: permission scope, spending limits, and required workflows
- [ ] You set (or know how to set) the organisation default workflow permission to **Read**
- [ ] You can describe the layout of an organisation-wide workflow library
- [ ] You identified at least one workflow in your repository that would be a good candidate for an organisation-wide required workflow
- [ ] You know where to find the organisation-level spending limit setting in the GitHub UI
- [ ] You can navigate to the organisation audit log and filter by workflow events

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

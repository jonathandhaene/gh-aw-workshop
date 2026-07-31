<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Production Readiness Checklist

> _A workflow that runs once in a demo is a proof of concept — a workflow that runs reliably every day for your whole team is production software. This step helps you cross that line._

## 🎯 What You'll Do

You'll walk through a structured production-readiness checklist that covers permissions, cost guardrails, observability, and governance. By the end, you'll have a workflow you can confidently hand off to a team or deploy org-wide.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow runs on a schedule and produces consistent output.
- _(Enterprise users)_ You have reviewed [Enterprise Setup Considerations](side-quest-enterprise-setup.md).

## Steps

### Security and permissions

Start with the smallest possible permission footprint.

Open your workflow's frontmatter and confirm `permissions:` is present and scoped tightly:

```yaml
---
permissions:
  contents: read
  issues: write
---
```

Grant only what each step needs. If you added MCP tools or cross-repository steps during the workshop, revisit those sections now and remove any permissions you no longer use.

> [!IMPORTANT]
> Over-permissioned workflows are one of the most common enterprise security findings. Lock down permissions before sharing a workflow with your team.

### Cost guardrails

Verify that your workflow has both a per-run and a daily spending limit. Open your frontmatter and confirm the following fields are set:

```yaml
---
max-ai-credits: 20
max-daily-ai-credits: 60
timeout-minutes: 10
---
```

Adjust the numbers to match your organisation's policy. If unsure, check with your GitHub administrator or refer to [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).

### Observability

Confirm that your workflow emits an artifact on every run. Open the workflow file and verify that an `artifacts:` block or a safe-output step creates a log entry or report file.

Then confirm: where do run logs live, how long are they retained (default 90 days; shorter on GHES), and who on your team can access them? If you cannot answer these questions, set up retention policies and access controls before rolling out more broadly.

### Documentation

Add a short `<!-- README -->` block at the top of your workflow's Markdown task brief. Describe what the workflow does, who owns it, and how to change the schedule or model:

```markdown
<!-- README
  Owner: @your-team
  Purpose: Posts a daily status summary to the team Slack channel.
  Schedule: Runs at 09:00 UTC on weekdays (cron: '0 9 * * 1-5').
  To change: Edit the `on.schedule` cron, update max-ai-credits, then compile.
-->
```

This comment is stripped at compile time and never reaches the runner, but it is invaluable for the next person who opens the file.

### Rollout plan

Before enabling the workflow org-wide, do a dry run:

1. Trigger the workflow manually from the **Actions** tab.
2. Review the run log and confirm the safe-output step completed successfully.
3. Ask one teammate to review the output and confirm it is useful.

Only then enable the production schedule.

> [!TIP]
> Start with a low-frequency schedule (weekly or daily at off-peak hours) and increase frequency once you are confident in the output quality and cost profile.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes `permissions:` scoped to only what is needed
- [ ] Your workflow frontmatter includes `max-ai-credits`, `max-daily-ai-credits`, and `timeout-minutes`
- [ ] You can locate your workflow's run artifacts in the GitHub Actions UI
- [ ] You have added a `<!-- README -->` comment documenting the workflow owner and purpose
- [ ] You triggered a manual run and reviewed the output with at least one teammate
- [ ] You are confident the workflow is ready to run on its production schedule

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

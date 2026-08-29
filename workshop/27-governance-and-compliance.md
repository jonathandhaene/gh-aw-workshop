<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Individual workflows are easy to trust — but when dozens of them run across an organisation, you need policies, visibility, and clear ownership._

## 🎯 What You'll Do

You will apply three enterprise governance practices to your agentic workflows: centralising runtime permissions through a reusable workflow, enabling organisation-level audit visibility, and documenting workflow ownership so your team knows who is responsible for each automation.

By the end of this step you will have a governance baseline that meets the requirements most enterprise security and compliance teams ask for.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have run your workflow at least once and reviewed its [audit report](25-audit-and-observability.md).
- _(Enterprise users)_ Your GitHub administrator has confirmed that GitHub Enterprise Cloud (GHEC) or GHES with Actions enabled is your organisation's platform.

## Steps

### Understand the three governance pillars

Enterprise teams typically ask three governance questions about any automation:

1. **Who owns it?** — Which team or person is responsible for this workflow?
2. **What can it do?** — Which permissions does it hold, and are they the minimum needed?
3. **What did it do?** — Is there an audit trail the security team can review?

The following steps address each pillar in order.

### Document ownership in your workflow file

Open your workflow `.md` file. Add an `owner:` comment near the top of the frontmatter so it is visible in `gh aw audit` reports and in code search.

```yaml
---
# owner: @your-team-or-github-handle
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
safe-outputs:
  - add_comment
---
```

The `owner:` comment is not a compiled field — it is a plain YAML comment that stays in the `.md` file for human readers and code-search queries.

> [!TIP]
> Pair the owner comment with a [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) entry for `.github/workflows/` so pull requests that change any workflow file automatically request a review from the right team.

### Verify minimal permissions

Inspect your frontmatter `permissions:` block. Confirm that every scope listed is actually used by the workflow:

- `contents: read` — required if the agent reads files or commits
- `contents: write` — required only if the agent commits; use `safe-outputs: push_to_branch` or `create_pull_request` instead where possible
- `issues: write` — required only if the agent creates or edits issues

Remove any scope you cannot justify with a specific tool call in the brief. Over-scoped workflows are the single most common finding in enterprise security reviews of agentic automations.

<details>
<summary>🖥️ Review permissions in the GitHub UI</summary>

1. Navigate to your repository on GitHub.
2. Click **Actions** in the top navigation, then select your workflow.
3. Open the most recent run and expand the **Set up job** step.
4. Look for the **GITHUB_TOKEN Permissions** section — it lists every scope the workflow requested.
5. Compare that list against your frontmatter `permissions:` block and remove any scope that appears but was never called.

</details>

### Enable organisation-level audit log visibility

If you are using GHEC or GHES, your GitHub administrator can enable organisation audit log streaming to export every workflow trigger, permission grant, and safe-output write to your SIEM or storage of choice.

As a workflow author, you can help that process by ensuring:

- Your workflow has a clear `name:` field — audit log entries use the workflow name as the identifier.
- Every `safe-outputs:` entry is scoped as narrowly as possible. The audit log records which safe-output type fired, so narrow scoping gives auditors actionable data.

Ask your administrator to confirm that [audit log streaming](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise) is enabled and that agentic workflow events are included.

> [!NOTE]
> On GitHub Enterprise Server, audit log streaming requires GHES 3.6 or later. Check your instance version before relying on streaming for compliance evidence.

### Run a governance self-check

Use `gh aw audit` to generate a report for your most recent run and check it against the governance checklist below.

```bash
gh aw audit <run-id>
```

Replace `<run-id>` with the run identifier from `gh aw logs`. The report lists permissions used, safe-output calls made, token counts, and any network domains contacted.

Review the report against these questions:

| Question | What to look for |
|----------|-----------------|
| Are permissions minimal? | No scope listed that was not exercised |
| Is the owner documented? | `owner:` comment in frontmatter |
| Are safe-outputs scoped? | No wildcard or over-broad safe-output type |
| Is the network bounded? | `network.allowed-domains` list present if the workflow fetches external URLs |
| Are costs within budget? | `max-ai-credits` and `max-daily-ai-credits` set |

If any row reveals a gap, fix it now before the workflow runs unattended in production.

## ✅ Checkpoint

- [ ] You added an `owner:` comment to your workflow frontmatter
- [ ] You audited your `permissions:` block and removed at least one unnecessary scope (or confirmed every scope is justified)
- [ ] You ran `gh aw audit` and reviewed the permissions and safe-outputs sections of the report
- [ ] You checked whether organisation-level audit log streaming is enabled (or noted it as a follow-up action for your administrator)
- [ ] You can explain, in one sentence, why minimal permissions matter for unattended agentic workflows

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

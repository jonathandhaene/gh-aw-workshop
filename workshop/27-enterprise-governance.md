<!-- page-journey: all -->
# Govern Agentic Workflows Across Your Organisation

> _Deploying agentic workflows at scale means more than writing great prompts — it means putting the right policies, approval gates, and visibility controls in place so your whole organisation can benefit safely._

## 🎯 What You'll Do

You will explore the controls available to GitHub Enterprise admins and workflow owners for governing agentic workflows across an organisation. By the end of this step you will know how to require approval before workflows run, how to restrict which models and tools are available, and where to find organisation-wide usage dashboards.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have access to your organisation's GitHub settings page, or you know who your GitHub Enterprise admin is.
- (Optional) Review [Enterprise Setup Considerations](side-quest-enterprise-setup.md) if your environment is GHES or GHEC.

## Understand the governance layers

Agentic workflow governance works at three levels. Each level narrows what a workflow can do.

| Layer | Who controls it | What it covers |
|-------|----------------|----------------|
| **Enterprise / GHES admin** | Enterprise or site admin | Feature enablement, allowed models, network egress policy |
| **Organisation settings** | Org owner | Approval requirements, runner policies, billing limits |
| **Workflow frontmatter** | Workflow author | Per-run cost caps, tool lists, safe-output allowlists, `network.allowed-domains` |

Think of these as nested fences: the enterprise admin sets the outer boundary, the org owner refines it, and every individual workflow tightens the fence further.

## Require workflow approval before runs

By default, new contributors' first workflow run requires approval. You can extend this policy to all outside contributors, or even to all users in a fork-based workflow.

Navigate to **Settings → Actions → General** in your organisation:

1. Under **Fork pull request workflows**, set the approval policy you want.
2. Under **Required approvals**, choose who must approve before a run starts.
3. Click **Save**.

> [!TIP]
> For agentic workflows that write back to your repository (issues, pull requests, comments), consider requiring approval for all first-time contributors. The agent acts on real data, so an untrusted prompt passed through a forked workflow could cause unintended writes.

![Organisation Actions settings page showing fork approval controls](images/27-org-actions-approval.png)

## Restrict which models are available

Enterprise admins can restrict which AI models workflows may use. This is configured in **Enterprise settings → Copilot → Policies → Model access**.

As a workflow author, you can reinforce this by specifying the `model` field explicitly in your workflow frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
model: copilot/gpt-4o
---
```

Pinning a specific model prevents the workflow from silently upgrading to a more expensive or less predictable model if org defaults change.

## Lock down network egress

The `network.allowed-domains` frontmatter key tells the agentic runtime which external hosts the agent may contact. Leaving this key empty (the default) restricts the agent to GitHub's own APIs.

For workflows that pull data from approved external sources, list only the domains you have vetted:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
network:
  allowed-domains:
    - api.github.com
    - status.example-internal.com
---
```

> [!NOTE]
> On GHES or GHEC, your enterprise admin may enforce an org-wide egress allowlist that further limits which domains appear in this list. Check with your admin before listing external domains.

## Review org-wide usage

Enterprise owners can view aggregate AI Credit (AIC) consumption across all repositories in the **Billing and plans** dashboard.

1. Navigate to your organisation on GitHub.
2. Click **Settings → Billing and plans → Usage**.
3. Filter by **Copilot** to see AIC consumption broken down by repository and user.

This view lets you spot unexpected cost spikes before they grow and identify which teams might benefit from tighter per-workflow `max-ai-credits` caps.

## Audit who changed what

Every agentic workflow run is recorded in the standard GitHub Actions audit log. Enterprise admins can export this log for compliance purposes.

1. Navigate to **Enterprise settings → Audit log**.
2. Filter by `action:workflows.*` or `action:copilot.*` to find agentic workflow events.
3. Export to CSV or stream to your SIEM using the [audit log streaming](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise) feature.

For per-run agent traces, refer to [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).

## ✅ Checkpoint

- [ ] You can name the three governance layers and explain which level controls each type of policy
- [ ] You located your organisation's Actions approval settings and can describe the fork approval policy in place
- [ ] You understand how pinning `model:` in frontmatter protects against silent model upgrades
- [ ] You added or reviewed the `network.allowed-domains` key in at least one of your workflow files
- [ ] You viewed the Billing and plans → Usage dashboard and can identify AIC consumption by repository
- [ ] You know how to access the enterprise audit log and which action filters to use for agentic workflow events
- [ ] You can explain what `safe-outputs` and `network.allowed-domains` accomplish at the workflow-author level

**Next:** You have reached the end of the advanced path — return to [What's Next? Keep Exploring](14-next-steps.md) to revisit any path you haven't tried yet, or explore the side quests linked from each step.

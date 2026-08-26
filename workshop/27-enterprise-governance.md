<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Shipping workflows is one thing — making sure every workflow in your organisation meets your security and compliance bar is another._

## 🎯 What You'll Do

You will configure the organisation-level controls that apply to every agentic workflow running under your GitHub organisation: Copilot usage policies, required approval gates for sensitive permissions, and runner group restrictions. By the end, you will have a governance layer that works whether your organisation is on GitHub.com, GitHub Enterprise Cloud (GHEC), or GitHub Enterprise Server (GHES).

## 📋 Before You Start

- You have a workflow running in GitHub Actions (completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)).
- You have **organisation owner** or **enterprise admin** access, or a colleague who does and can walk through these settings with you.
- You know which GitHub deployment your organisation uses: github.com, GHEC, or GHES 3.12+.

## Enable or restrict Copilot for the organisation

Agentic workflows run through the **Copilot cloud agent**. Organisation owners control whether and how Copilot is available to Actions workflows.

### Check the organisation Copilot policy

1. Go to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Under **Copilot in GitHub Actions**, confirm the setting is **Enabled** or **Enabled for selected repositories**.

> [!NOTE]
> On GHES, this setting may appear under **Admin** → **Copilot** depending on your instance version. Ask your site administrator if you cannot find it.

If your organisation restricts Copilot access to specific repositories, agentic workflows in unrestricted repositories will fail at the agent step with a permissions error — even if the workflow YAML is correct. Add your workflow repository to the allow-list before testing.

### GHEC: confirm Copilot Enterprise licensing

On GitHub Enterprise Cloud, the Copilot cloud agent requires a **Copilot Enterprise** licence assigned to the GitHub Actions bot identity or the runner service account.

1. In **Settings** → **Billing & licensing**, confirm at least one Copilot Enterprise seat is available.
2. Under **Copilot** → **Seat management**, verify that the seat policy covers Actions-triggered workflows (check with your GitHub account manager if unsure).

## Require approval gates for sensitive permissions

Some agentic workflows write to repositories, create issues, or post comments. These actions are powerful — and easy to get wrong. Approval gates add a human checkpoint before sensitive runs proceed.

### Set required reviewers on an environment

1. In the **repository** that owns the workflow, go to **Settings** → **Environments**.
2. Click **New environment** (or open an existing one, for example `production`).
3. Under **Deployment protection rules**, enable **Required reviewers** and add the reviewers or teams who must approve each run.
4. In your workflow frontmatter, reference the environment:

```yaml
---
name: Sensitive Report Publisher
on:
  schedule: weekly
environment: production
permissions:
  contents: write
  issues: write
---
```

When this workflow triggers, GitHub pauses it and emails the required reviewers. The agent step only runs after a reviewer approves.

> [!TIP]
> Use environments sparingly. Reserve required reviewers for workflows that write to production branches, send external notifications, or create issues on behalf of the organisation.

## Restrict which runners can execute agentic workflows

Runner groups let you control which workflows can use which machines — a critical control for GHES and GHEC deployments where self-hosted runners run inside a private network.

### Create a runner group for agentic workflows

1. Go to your organisation → **Settings** → **Actions** → **Runner groups**.
2. Click **New runner group** and name it (for example, `agentic-approved`).
3. Under **Repository access**, select **Selected repositories** and add only the repositories you trust to run agentic workloads.
4. Add your self-hosted runners to this group.

### Point your workflow at the group

In your workflow frontmatter, set the `runs-on` key to match the group label you assigned to those runners:

```yaml
---
name: Governed Agentic Report
on:
  schedule: daily on weekdays
runs-on: self-hosted
---
```

> [!NOTE]
> On GHES, runner groups are the primary mechanism for network segmentation. Agentic workflows that call MCP servers or external APIs should run on runners with explicit egress allowlists. Work with your network team to confirm outbound rules before enabling MCP-connected workflows at scale.

## Audit organisation-wide agentic workflow activity

Beyond per-run audit logs (covered in [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)), organisation owners can review aggregate activity.

1. Go to your organisation → **Settings** → **Audit log**.
2. Filter by **Copilot** or **Actions** events to see which workflows triggered agent sessions, how often, and under which actor.
3. Export the log as CSV for compliance records if required by your organisation's policy.

<details>
<summary>🖥️ GHES audit log location</summary>

On GHES, the organisation audit log is at **Admin** → **Audit log** in the site admin panel. The same event types apply, but the UI path differs. Enterprise administrators can also stream audit logs to a SIEM using the **Log streaming** feature (requires GHES 3.12+ with audit log streaming enabled).

</details>

## ✅ Checkpoint

- [ ] You confirmed the Copilot in GitHub Actions policy is enabled for your organisation
- [ ] You identified any repository-level restrictions that would block agentic workflow execution
- [ ] You set up (or reviewed) an environment with required reviewers for at least one sensitive workflow
- [ ] You reviewed runner group configuration and confirmed agentic workflows are assigned to an appropriate group
- [ ] You located the organisation audit log and filtered for Copilot or Actions events

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

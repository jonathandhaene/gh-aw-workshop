<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows are powerful — and with that power comes the need for clear guardrails that every team in your org can rely on._

## 🎯 What You'll Do

You will configure organisation-level governance controls for agentic workflows: required permissions policies, approval gates for sensitive operations, and audit log routing. By the end, you will have a governance checklist your security or platform team can review, and you will understand how enterprise features map to the workflow frontmatter your team already writes.

## 📋 Before You Start

- You have a working agentic workflow (see [Test and Improve Your Workflow](12-test-and-iterate.md)).
- You have reviewed cost controls and audit logging (see [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)).
- You have organisation Owner or Admin access on GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) to apply org-level policies.

## Steps

### Review the default permissions model

Every agentic workflow run inherits the repository's `GITHUB_TOKEN` permissions. The gh-aw runtime further constrains what the agent can write through [safe-outputs](https://github.github.com/gh-aw/reference/safe-outputs/) — the agent declares its intended write actions and the runtime enforces them.

Open your workflow file and confirm it has an explicit `permissions:` block:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
---
```

Prefer **least-privilege**: only grant the permissions the workflow actually needs. Avoid `contents: write` unless the workflow must push files.

<details>
<summary>🖥️ Review permissions in the GitHub UI</summary>

1. Navigate to your repository on GitHub.
2. Click **Actions** in the top navigation.
3. Select a recent run of your workflow and click a job.
4. Expand the **Set up job** log section.
5. Look for the `Token Permissions` block — it lists every permission granted to `GITHUB_TOKEN` for that run.

</details>

### Require approvals for write-capable workflows

For workflows that can write to issues, pull requests, or repository contents, add a **required reviewer** using a GitHub [deployment environment](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment):

1. In your repository, go to **Settings** → **Environments** → **New environment**.
2. Name the environment `agentic-writes` (or your organisation's convention).
3. Under **Required reviewers**, add your security team or a named lead.
4. Update your workflow frontmatter to target the environment:

```yaml
---
name: Sensitive Workflow
on:
  workflow_dispatch:
environment: agentic-writes
permissions:
  issues: write
---
```

Now every run that can write pauses for approval before the agent executes.

> [!NOTE]
> The `environment:` key is standard Actions YAML — agentic workflows inherit this mechanism with no extra syntax required.

### Enforce org-wide policies

GitHub Enterprise Cloud lets organisation admins restrict which repositories can run agentic workflows and which models they can use.

1. Navigate to your **Organisation Settings** → **Actions** → **General**.
2. Under **AI-powered features**, review the model allowlist. Restrict to approved models if your organisation has a procurement or data-residency requirement.
3. Under **Runner groups**, confirm that agentic workflows route to the correct runner group. For GHES, verify that the runner group's network policy allows outbound connections to your model provider.

> [!TIP]
> See [Self-Hosted Runner Configuration](https://github.github.com/gh-aw/reference/self-hosted-runners/) for the full list of proxy and firewall requirements for air-gapped GHES deployments.

### Route audit logs to your SIEM

The `gh aw audit` report produced in [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) is stored as a workflow artifact. For enterprise compliance you may also want these events in your Security Information and Event Management (SIEM) system.

Enable **audit log streaming** for your organisation:

1. Go to **Organisation Settings** → **Audit Log** → **Log streaming**.
2. Add a streaming endpoint (Splunk, Azure Monitor, Datadog, Amazon S3, or Google Cloud Storage are natively supported).
3. Filter on event category `workflows` and action prefix `agentic_` to isolate agentic workflow events.

Your SIEM now receives a structured event for every workflow run, including the model used, token count, safe-output declarations, and outcome.

### Document your governance baseline

Commit a short policy document to your repository at `.github/agentic-workflows-policy.md`. It should record:

- Approved models and the rationale for each.
- Maximum `max-ai-credits` per run and per day.
- Environments that require approval gates.
- The team responsible for reviewing `gh aw audit` reports.

This document does not need to be a formal standard — a concise, honest record of your team's decisions is enough. Future auditors (and future you) will thank you.

## ✅ Checkpoint

- [ ] Your workflow frontmatter has an explicit `permissions:` block with least-privilege grants
- [ ] You can view the `Token Permissions` section in an Actions run log
- [ ] You created (or identified an existing) deployment environment with required reviewers for write-capable workflows
- [ ] You reviewed the model allowlist in your organisation's Actions settings
- [ ] Audit log streaming is enabled (or you have documented why it is deferred)
- [ ] A `.github/agentic-workflows-policy.md` file exists with your team's governance baseline

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

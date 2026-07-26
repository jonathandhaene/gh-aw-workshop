<!-- page-journey: enterprise -->
# Govern Your Agentic Workflows: Policy, Approval, and Compliance

> _Agentic workflows act on your behalf — so your organisation needs clear guardrails about who can create them, what they can do, and how their actions are recorded._

## 🎯 What You'll Do

You will map the GitHub policy controls that govern agentic workflows in your organisation, verify that your workflow runs within those boundaries, and confirm that a complete audit trail is available for compliance review.

## 📋 Before You Start

- You have a running agentic workflow from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have reviewed cost controls in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have organisation owner or admin access, or a contact who does.

## Understand the policy model

Agentic workflows run as GitHub Actions jobs. The Copilot cloud agent executes inside that job using a scoped token. Three layers of policy control govern what it can do.

| Layer | Where it is set | What it controls |
|---|---|---|
| **Copilot access policy** | Organisation → Copilot → Policies | Which members can request Copilot completions |
| **Actions permissions** | Organisation or repository → Actions → General | Which workflows are allowed to run, and from which forks |
| **Workflow token permissions** | `permissions:` block in the workflow file | Which GitHub API scopes the agent token carries |

All three layers must allow an action before the agent can perform it. If any layer denies it, the step fails.

### Check your Copilot access policy

1. Navigate to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Confirm that **Copilot in GitHub Actions** is set to **Enabled** (or **Enabled for selected members**, depending on your rollout plan).

> [!NOTE]
> On GitHub Enterprise Server, this policy may be called **Copilot cloud agent** and may require an administrator to enable it at the enterprise level before it appears in organisation settings.

### Review workflow token permissions

Open your workflow `.md` file and check the `permissions:` block in the frontmatter.

```yaml
---
permissions:
  contents: read
  issues: write
  pull-requests: read
---
```

Apply the **principle of least privilege**: only list the scopes your task brief actually needs. Remove `write` access for resources the agent reads but does not modify.

<details>
<summary>🖥️ Verifying permissions in the GitHub UI</summary>

1. Open the completed workflow run in **Actions**.
2. Click the job name → expand the **Set up job** step.
3. Find the **GITHUB_TOKEN Permissions** section — it lists the exact scopes the token carried for that run.

</details>

### Enable required approvals (optional, enterprise)

For sensitive workflows — those that push code, merge PRs, or interact with external services — you can require a human review before the run begins.

1. Go to **Settings** → **Environments** → **New environment** (e.g. `agentic-prod`).
2. Enable **Required reviewers** and add the team that must approve.
3. Reference the environment in your workflow frontmatter:

```yaml
---
environment: agentic-prod
---
```

Now every run pauses for approval before the agent executes.

## Confirm your audit trail

Refer to [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) for the full walkthrough. At minimum, confirm:

- Workflow run logs are retained and searchable in **Actions**.
- The `gh aw audit` report for your workflow is readable and includes AIC consumption, tool calls, and safe-output actions.

> [!TIP]
> Export audit reports as workflow artifacts and archive them to a storage bucket if your organisation's compliance policy requires retention beyond GitHub's 90-day default.

## ✅ Checkpoint

- [ ] You located your organisation's Copilot access policy and confirmed agentic workflows are enabled
- [ ] You reviewed the `permissions:` block in your workflow and removed any unnecessary write scopes
- [ ] You can explain which three policy layers govern what the Copilot cloud agent can do
- [ ] You know how to add required reviewers to a GitHub environment and reference it from a workflow
- [ ] You confirmed that audit logs from a past run are available and include AIC usage

<!-- journey: enterprise -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

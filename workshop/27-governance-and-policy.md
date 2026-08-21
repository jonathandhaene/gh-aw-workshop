<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Powerful AI automation deserves clear guardrails — this step shows you how to set organizational policies so your team can adopt agentic workflows confidently and responsibly._

## 🎯 What You'll Do

You'll define a lightweight governance strategy for agentic workflows in your organization. By the end of this step you'll have reviewed the key policy levers available to GitHub Enterprise Cloud and GitHub Enterprise Server administrators, mapped each control to a risk it mitigates, and documented a one-page policy brief your team can adopt.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin or org-owner access to a GitHub organization, **or** you are preparing a recommendation for someone who does.
- _(GHES users)_ Your instance is on GHES 3.12 or later and the Copilot Enterprise feature is enabled by your site administrator.

## Steps

### Understand the governance landscape

Agentic workflows run as automation that can read repository content, call external APIs, and write back to GitHub (issues, comments, PRs). That power introduces four categories of risk your governance policy should address:

| Risk | Description |
|------|-------------|
| **Unauthorized access** | A workflow reads secrets or data it should not see |
| **Unintended writes** | An agent posts a comment or merges a PR without human review |
| **Cost overruns** | Many scheduled workflows accumulate unexpected AIC charges |
| **Prompt injection** | Malicious content in issues or PR descriptions hijacks the agent |

gh-aw is designed so each of these risks has a corresponding control you can enforce.

### Map controls to risks

Review the control surface available to org administrators:

| Control | Where it lives | Risk it addresses |
|---------|---------------|-------------------|
| `permissions:` in frontmatter | Workflow file | Unauthorized access |
| `safe-outputs:` allowlist | Workflow file | Unintended writes |
| `max-ai-credits` / `max-daily-ai-credits` | Workflow file | Cost overruns |
| `network.allowed-domains` | Workflow file | Prompt injection via external fetch |
| Required reviewers on workflow file changes | Branch protection rule | All of the above |
| Copilot policy settings | Organization → Settings → Copilot | Model access, who can create workflows |
| GHEC/GHES audit log | Organization → Settings → Audit log | Visibility and incident response |

> [!NOTE]
> On GitHub Enterprise Server, the site administrator can also disable Copilot for agentic workflows at the instance level. Individual organization owners cannot override that setting.

### Set branch protection on your workflows folder

The simplest org-wide governance control is requiring a pull request review before any workflow file reaches your default branch. This means no agentic workflow can be deployed without a second pair of eyes.

In the GitHub UI:

1. Go to your repository (or the shared `workflow-templates` repository you created in [Share and Reuse](18-share-and-reuse.md)).
2. Click **Settings** → **Branches**.
3. Under **Branch protection rules**, click **Add rule**.
4. Set **Branch name pattern** to `main` (or your default branch name).
5. Enable **Require a pull request before merging** and set **Required approvals** to `1`.
6. Enable **Restrict who can push to matching branches** if you want to limit who can bypass the rule.
7. Click **Save changes**.

![Branch protection rule for workflow files](images/27-branch-protection.png)

> [!TIP]
> For a dedicated `workflow-templates` repository, consider requiring **CODEOWNERS** review so that a designated workflow author must approve every change.

### Configure Copilot policy settings (org admins)

Org owners on GitHub Enterprise Cloud can control which members can create and run agentic workflows:

1. Go to your organization on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Review the **Agentic workflows** section and choose between:
   - **Enabled for all members** — any member can create and run agentic workflows.
   - **Enabled for selected members** — restrict to a trusted set of teams or individuals.
   - **Disabled** — no agentic workflows can run in this organization.

> [!NOTE]
> Policy changes take effect immediately for new workflow runs. Existing scheduled workflows that are already queued may still run under the previous policy for up to a few minutes.

### Draft a one-page policy brief

Governance works best when expectations are written down. Draft a `AGENTIC-WORKFLOWS-POLICY.md` file in your shared `workflow-templates` repository (or in your org's `.github` repository so it appears as a default community health file).

A minimal policy brief covers:

- **Who can create agentic workflows** (all engineers, a core platform team, etc.)
- **Required review process** (PR review, security review for external API access)
- **Mandatory frontmatter controls** (`max-ai-credits`, `safe-outputs:` allowlist required for all workflows that write)
- **Prohibited actions** (writing to production systems, accessing secrets not scoped to the workflow)
- **Incident response** (who to contact if a workflow produces unexpected output)

<details>
<summary>📄 Minimal policy brief template</summary>

```markdown
# Agentic Workflows Policy

**Owner:** Platform Engineering
**Last reviewed:** YYYY-MM-DD

## Who can create workflows
All engineers may author agentic workflows in their own repositories.
Workflows that write to shared resources require review from the Platform Engineering team.

## Required controls
Every agentic workflow must include:
- `max-ai-credits:` set to a per-run limit appropriate for the task
- `safe-outputs:` listing only the write operations the workflow needs
- `permissions:` scoped to the minimum required (avoid `contents: write` unless necessary)

## Prohibited actions
- Accessing secrets beyond those explicitly scoped to the workflow
- Writing to production databases or external systems without explicit approval
- Removing or bypassing `safe-outputs` restrictions

## Review process
1. Author submits a PR adding or changing the workflow file.
2. A second engineer approves the PR.
3. Workflows that call external APIs require a security review comment from the Platform team.

## Incident response
Contact **#platform-engineering** in Slack or open an issue in `your-org/workflow-templates`.
```

</details>

## ✅ Checkpoint

- [ ] You can name all four risk categories and their corresponding gh-aw controls
- [ ] You have set (or know how to set) a branch protection rule on your workflows folder
- [ ] You have reviewed the Copilot policy settings available to org admins on GHEC or GHES
- [ ] You have drafted or reviewed a one-page agentic workflows policy brief for your team
- [ ] You can explain the difference between per-workflow controls (frontmatter) and org-level controls (policy settings and branch protection)

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to pick your next adventure, or share your finished policy brief with a teammate and ask them to review it against the risk table above.
<!-- /journey -->

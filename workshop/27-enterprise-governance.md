<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Running AI-powered workflows at scale requires more than technical setup — you need org-wide policy controls that keep automations trusted, auditable, and within approved boundaries._

## 🎯 What You'll Do

You will configure organisation-level policy settings for agentic workflows, set required reviewers on sensitive workflow runs, and document a governance checklist your team can use before promoting a workflow to production.

## 📋 Before You Start

- You have at least one agentic workflow running successfully (see [Test and Improve Your Workflow](12-test-and-iterate.md)).
- You have Owner or Admin access to a GitHub organisation, or your admin can apply the settings on your behalf.
- If you are on GitHub Enterprise Server, your instance must be version 3.12 or later (see [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md)).

## Steps

### Understand the governance model

Agentic workflows run inside GitHub Actions. Every governance mechanism that applies to Actions applies here too — plus a few gh-aw-specific controls.

The three layers of governance are:

1. **Organisation policies** — control which repositories can run Copilot-powered workflow steps.
2. **Repository permissions** — restrict which branches can trigger workflow runs and who can approve them.
3. **Workflow frontmatter** — limit what the agent itself can do during a run (permissions, budgets, timeouts).

Think of them as nested fences: org policies set the outer boundary, repository settings narrow it further, and the workflow file defines the innermost constraint.

### Review Copilot-in-Actions org policy

GitHub Enterprise Cloud (GHEC) lets org owners control whether Copilot can be used inside workflow runs.

To review the policy:

1. Go to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Under **Copilot in GitHub Actions**, check whether the policy is set to **Allowed**, **Disabled**, or **No policy** (inherits enterprise default).

> [!NOTE]
> On GitHub Enterprise Server, this setting may be controlled at the enterprise level by your site administrator. Ask your admin whether Copilot-powered Actions are enabled before building agentic workflows on GHES.

Set the policy to **Allowed** for repositories where your team actively uses agentic workflows, and consider leaving it as **Disabled** for sensitive repositories where you want human-only automation.

### Restrict which branches can trigger workflows

Agentic workflows often use `pull_request` or `push` triggers. To prevent untrusted branches from triggering AI-powered runs:

1. In your repository, go to **Settings** → **Actions** → **General**.
2. Under **Fork pull request workflows**, choose **Require approval for first-time contributors** (or stricter, depending on your team's threat model).
3. For organisation repositories, also set **Actions permissions** to restrict which Actions and workflows are allowed to run.

> [!TIP]
> For workflows that write back to the repository (such as a PR reviewer that posts comments), apply the principle of least privilege: set `permissions: pull-requests: write` only on those specific workflows, and leave the org default at `read-all`.

### Add required reviewers to sensitive environments

GitHub's **Environments** feature lets you add human approval gates before a job runs. Use this for agentic workflows that can modify repository content, post external comments, or trigger downstream systems.

To create a protected environment:

1. Go to **Settings** → **Environments** → **New environment**.
2. Name it something descriptive, such as `ai-workflows-prod`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add the team members who should approve runs.
4. In your workflow's frontmatter, reference the environment:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
  workflow_dispatch: {}
jobs:
  report:
    runs-on: ubuntu-latest
    environment: ai-workflows-prod
---
```

With this in place, every scheduled run will pause for human approval before the agent step executes. This is particularly useful for workflows that post to Slack, create issues, or modify project boards.

### Document your workflow's governance profile

Before promoting any agentic workflow to production, run through this checklist. You can file it as a GitHub issue in your repository or store it as a comment at the top of the workflow `.md` file.

**Workflow governance checklist:**

- [ ] The workflow's `permissions:` block grants only the scopes it genuinely needs.
- [ ] `max-ai-credits` is set in the frontmatter and reviewed quarterly.
- [ ] `timeout-minutes` is set to prevent runaway runs.
- [ ] The trigger scope is as narrow as practical (specific branches, required labels, or schedule only).
- [ ] A protected environment with required reviewers is configured for any workflow that can write to external systems.
- [ ] The workflow has been run manually at least once and the audit artifact reviewed (see [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)).
- [ ] At least one team member other than the author has reviewed the agent brief.

### Share governance policy with your team

Governance only works when the whole team knows the rules. A practical way to distribute this is to store the checklist in a `.github/agentic-governance.md` file in your organisation's `.github` repository. GitHub automatically surfaces files in that repository to all org members.

<details>
<summary>🖥️ Creating the governance policy file in the GitHub UI</summary>

1. Navigate to your organisation's `.github` repository (create it at **github.com/new** if it doesn't exist yet — name it exactly `.github`).
2. Click **Add file** → **Create new file**.
3. Name the file `agentic-governance.md`.
4. Paste your governance checklist and team-specific rules.
5. Click **Commit new file**.

</details>

## ✅ Checkpoint

- [ ] You have reviewed the Copilot-in-Actions policy for your organisation and know its current setting
- [ ] You have configured (or verified) branch protection rules for at least one workflow trigger
- [ ] You have created a protected environment with required reviewers for a production-facing workflow
- [ ] Your workflow frontmatter includes `permissions:`, `max-ai-credits`, and `timeout-minutes`
- [ ] You have a governance checklist document your team can reference before promoting new workflows

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

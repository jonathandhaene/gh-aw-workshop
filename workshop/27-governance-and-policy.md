<!-- page-journey: all -->
# Govern Your Agentic Workflows at Scale

> _When agentic workflows move from your personal repository into a shared team or enterprise environment, you need guardrails — not just good intentions._

## 🎯 What You'll Do

You will explore the org-level and enterprise-level controls that govern how agentic workflows are approved, scoped, and monitored across repositories. By the end you will have a mental model for deploying agentic workflows responsibly in an enterprise environment and know which settings to check before rolling out to a wider team.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have owner or admin access to a GitHub organization, **or** you can read along and note the settings to discuss with your GitHub administrator.

> [!NOTE]
> GHES users: some settings described here (such as Copilot policy scopes) are managed by your site admin in the enterprise-level admin console. Refer to the [Enterprise Setup Considerations](side-quest-enterprise-setup.md) side quest for GHES-specific paths.

## Understand the Three Governance Layers

Agentic workflow governance works at three levels. Think of them as concentric circles:

1. **Workflow file** — the author controls what permissions, tools, and safe-outputs the workflow requests. This is the innermost circle and always applies.
2. **Repository / organization** — an admin can restrict which workflows are allowed to run, which Copilot models are available, and whether workflow-triggered write operations need review.
3. **Enterprise** — a GitHub Enterprise admin can set policies that override org settings: disabling Copilot in Actions entirely, restricting models, or requiring approval before agentic jobs run.

Understanding which layer controls each setting prevents confusion when a workflow that works in your personal repo fails in a shared org.

## Review Org-Level Actions Policies

Open your organization's Actions settings and confirm:

1. In your organization, click **Settings** → **Actions** → **General**.
2. Under **Workflow permissions**, set the default token permission to **Read repository contents and packages**. This ensures agentic workflows cannot silently write beyond what their `permissions:` block explicitly requests.
3. Under **Fork pull request workflows**, confirm **Require approval for first-time contributors** is enabled. This prevents an external contributor's pull request from accidentally triggering your PR reviewer workflow.

![Org Actions permissions panel showing read-only default token](images/27-org-actions-permissions.svg)

<details>
<summary>🖥️ Where to find enterprise-level controls</summary>

On GitHub Enterprise Cloud, go to your enterprise account → **Policies** → **Actions**. Here you can:

- Disable or restrict Actions across all organizations.
- Limit which reusable workflows and agentic extensions are allowed.
- Require enterprise-managed identities (EMU) for all Actions runs.

On GHES, the site admin accesses equivalent controls under **Admin** → **Policies** → **Actions**.

</details>

## Restrict Which Models Your Org Can Use

Agentic workflows can request specific AI models via the `engine:` field in frontmatter. Enterprise admins can restrict which models are available to Actions runners:

1. Go to **Settings** → **Copilot** at the organization level.
2. Review the list of models permitted for GitHub Actions.
3. If a model your workflow relies on is not listed, contact your GitHub administrator to request access.

## Require Pull Request Review for Workflow Changes

Because a workflow's task brief determines what the agent does, changes to `.github/workflows/*.md` deserve the same review rigor as application code.

Set up a **CODEOWNERS** rule to require review for all workflow files:

1. Open or create `.github/CODEOWNERS` in your repository.
2. Add a line like:

   ```text
   .github/workflows/ @your-org/workflow-owners
   ```

3. In **Settings** → **Branches**, create or update a branch protection rule for your default branch to **require a pull request** and **require review from Code Owners**.

Now any change to an agentic workflow file must be approved by a designated team before it can reach the main branch.

## ✅ Checkpoint

- [ ] You can name the three governance layers and explain which controls live at each level
- [ ] You verified (or know the location of) the default workflow permissions setting in your org or enterprise
- [ ] You confirmed fork pull request approval is enabled for your org
- [ ] You know how to find the Copilot model allow-list for GitHub Actions in your org or enterprise settings
- [ ] You added or reviewed a `CODEOWNERS` entry for `.github/workflows/`
- [ ] You know who to contact in your enterprise to change Actions or Copilot policies

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

<!-- page-journey: all -->
<!-- page-adventure: main -->
# Enterprise Governance for Agentic Workflows

> _Putting guardrails around agentic workflows ensures they stay auditable, cost-controlled, and safe to run at scale — without slowing down the teams that depend on them._

## 🎯 What You'll Do

You will configure the three governance layers that enterprise teams care about most: permission boundaries, branch-protection rules that gate automated writes, and an organisation-level policy that decides which workflows are allowed to run. By the end, you'll have a workflow that respects enterprise constraints and a governance checklist you can reuse for every new workflow you deploy.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow can run manually and produces a safe-output (issue, comment, or similar).
- If you are on GitHub Enterprise Server, confirm your instance is GHES 3.12 or later (see [Enterprise Setup Considerations](side-quest-enterprise-setup.md) if you haven't already).

## Steps

### Scope workflow permissions to the minimum needed

Agentic workflows run with the `GITHUB_TOKEN` by default, which can be overly broad. Locking down permissions in the frontmatter is the single highest-impact governance action you can take.

Open your workflow `.md` file and add an explicit `permissions:` block in the frontmatter:

```yaml
---
name: Daily Standup Summary
on:
  schedule:
    - cron: "0 9 * * 1-5"
permissions:
  contents: read
  issues: write
  pull-requests: read
---
```

Grant only the scopes the workflow actually uses. A workflow that only comments on issues does not need `contents: write`.

> [!TIP]
> The [gh-aw permissions reference](https://github.github.com/gh-aw/reference/permissions/) lists every scope and its effect. Review it whenever you add a new safe-output type to a workflow.

After editing, recompile:

```bash
gh aw compile
```

### Restrict automated writes with branch protection

When an agentic workflow pushes changes (for example, updating a file, merging a PR, or tagging a release), you want at least one human to review that output before it lands on a protected branch.

In your repository on GitHub:

1. Go to **Settings** → **Branches**.
2. Click **Add branch protection rule** (or edit an existing rule for `main`).
3. Enable **Require a pull request before merging**.
4. Optionally enable **Require review from Code Owners** for files that are especially sensitive.
5. Click **Save changes**.

Now any workflow that opens a pull request still works, but merging requires human approval. Workflows that push directly to `main` will be blocked — which is the intended behaviour.

> [!NOTE]
> On GHEC, branch protection rules can be centrally managed through **repository rulesets** at the organisation level. If your organisation uses rulesets, check with your GitHub administrator before adding repository-level rules to avoid conflicts.

### Enable required workflow approval for first-time contributors

If external contributors or new team members can trigger your workflows, add a layer of approval before workflows run on their behalf.

Go to **Settings** → **Actions** → **General** → **Fork pull request workflows from outside collaborators** and select **Require approval for first-time contributors**. This prevents an untrusted commit from immediately triggering an agentic workflow that has write permissions.

### Review your organisation's agentic workflow policy

Enterprise organisations can control which repositories may use agentic workflows and which AI models are permitted.

Ask your GitHub administrator to confirm:

- Whether [Copilot agentic workflows are enabled](https://github.github.com/gh-aw/reference/permissions/) for your organisation.
- Which AI models are allowed (some enterprises restrict models through Copilot policy).
- Whether there is an approved model list in the organisation's Copilot settings.

If you manage the organisation yourself:

1. Go to your organisation on GitHub → **Settings** → **Copilot** → **Policies**.
2. Confirm agentic workflow access is enabled for the repositories in scope.
3. Review the allowed model list and remove any models that do not meet your data-residency or compliance requirements.

### Document your governance decisions

A short comment block at the top of your workflow `.md` file is the most durable record of why each governance choice was made:

```yaml
---
# Governance note: read-only on contents, write on issues only.
# Branch protection on main requires human PR approval for any automated writes.
# Approved at: 2024-06-01 by @platform-team
permissions:
  contents: read
  issues: write
---
```

This comment travels with the workflow through version control and appears in audit diffs.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes an explicit `permissions:` block with only the scopes it needs
- [ ] You ran `gh aw compile` after updating the frontmatter and the `.lock.yml` was regenerated
- [ ] Branch protection is enabled on your main branch with pull-request review required
- [ ] You confirmed your organisation's Copilot policy allows agentic workflows in your repository
- [ ] You can name the three governance layers covered in this step (permissions, branch protection, org policy)
- [ ] You added (or can describe) a governance comment block to your workflow file

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

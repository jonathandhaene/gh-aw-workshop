<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at Scale

> _As agentic workflows multiply across your organisation, governance — consistent policies for access, auditability, and safe AI use — is what keeps them trustworthy and maintainable._

## 🎯 What You'll Do

You'll learn how to apply organisation-level policies to agentic workflows: restricting which models can be used, enforcing required permissions, and establishing a lightweight review process before workflows reach production. By the end, you'll have a governance checklist you can hand to a team or embed in a pull request template.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one agentic workflow already running in your repository.
- _(Enterprise users)_ You have organisation-owner or admin access, or you are working with someone who does.

## Steps

### Understand what governance covers

Governance for agentic workflows spans three areas:

1. **Access control** — who can create, edit, and trigger workflows that use AI.
2. **Policy enforcement** — which models, tools, and permissions are allowed.
3. **Auditability** — capturing enough context in run artifacts to answer "what did the agent do, and why?"

These are extensions of standard GitHub Actions governance, so policies you already apply to workflows (required reviewers, branch protection, environment approvals) carry over directly.

### Apply organisation-level Copilot policies

GitHub lets organisation owners restrict which Copilot features are available. For agentic workflows:

1. In your organisation, go to **Settings** → **Copilot** → **Policies**.
2. Review the **Agentic workflows** section. You can:
   - Enable or disable agentic workflow runs entirely.
   - Restrict which models agents may use (e.g. lock to a specific Copilot-hosted model).
   - Control whether agents can access external MCP servers.
3. Click **Save** after making any change.

> [!NOTE]
> On GitHub Enterprise Server (GHES), Copilot policy settings may be managed at the enterprise level. Check with your site administrator if you do not see these options.

![Copilot policy settings showing agentic workflow controls](images/27-copilot-policy-settings.png)

### Lock the model in your workflow frontmatter

Beyond org-level policy, you can pin the model inside your workflow file so reviewers and auditors know exactly which model was used for every run:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
engine: copilot
model: gpt-4o
permissions:
  contents: read
  issues: write
---
```

Pinning the model prevents silent upgrades from changing output behaviour between runs — especially important in regulated environments.

### Enforce least-privilege permissions

Each workflow should declare the minimum set of permissions it needs. Overly broad permissions are a common audit finding:

```yaml
permissions:
  contents: read      # read repository data
  issues: write       # post the daily summary as an issue comment
```

Avoid `permissions: write-all`. If a reviewer sees it, flag it for a permissions audit.

> [!TIP]
> The [gh-aw permissions reference](https://github.github.com/gh-aw/reference/permissions/) lists every permission scope and explains which tools require each one.

### Require pull request review before production

Protect your default branch so agentic workflow changes go through review:

1. In your repository, go to **Settings** → **Branches** → **Add branch ruleset** (or edit an existing rule for `main`).
2. Enable **Require a pull request before merging**.
3. Set **Required approvals** to at least **1**.
4. Optionally, add a **CODEOWNERS** entry for `.github/workflows/` pointing to a security or platform team:

```
.github/workflows/ @your-org/platform-team
```

This ensures someone with security context reviews every new or changed workflow before it runs in production.

### Create a lightweight governance checklist

A pull request template makes the review consistent. Create `.github/pull_request_template.md` (or a workflow-specific template) with a checklist reviewers fill in:

```markdown
## Agentic Workflow Review

- [ ] Permissions are scoped to the minimum required
- [ ] Model is pinned to an approved model
- [ ] No external MCP servers are used without security review
- [ ] `max-ai-credits` and `max-daily-ai-credits` are set
- [ ] Safe-outputs limit is set (e.g. `create-issue: limit: 1`)
- [ ] Task brief does not include hard-coded secrets or tokens
- [ ] A manual test run was completed and the run log was reviewed
```

<details>
<summary>🖥️ Create the template in the GitHub UI</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. In the name field, type `.github/pull_request_template.md`.
3. Paste the checklist above into the editor.
4. Click **Commit new file**.

</details>

### Review an existing workflow against the checklist

Open your `daily-status.md` or another workflow you have already built. Work through the checklist:

- Is `permissions:` present and minimal?
- Is `max-ai-credits` set?
- Is `safe-outputs:` configured with an appropriate limit?
- Is the model pinned or policy-controlled?

Fix any gaps you find, then commit and push.

## ✅ Checkpoint

- [ ] You reviewed your organisation's Copilot policy settings and can describe what each agentic-workflow policy controls
- [ ] Your workflow frontmatter includes an explicit `model:` pin and a minimal `permissions:` block
- [ ] Branch protection on your default branch requires at least one reviewer for changes to `.github/workflows/`
- [ ] You created (or reviewed) a PR template with the agentic workflow governance checklist
- [ ] You audited at least one existing workflow against the checklist and addressed any gaps
- [ ] You can explain the difference between org-level Copilot policy and per-workflow frontmatter controls

<!-- journey: all -->
**Next:** [Return to What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

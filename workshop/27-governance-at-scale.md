# Govern Your Agentic Workflows Across a Team or Organisation

> _Agentic workflows that run unattended in CI can have real consequences — governance turns good intentions into enforceable team standards._

## 🎯 What You'll Do

Add lightweight governance controls so your agentic workflows follow team or enterprise policy: protect sensitive files from accidental overwrites, require review before workflows gain new write permissions, and document a runbook your colleagues can trust.

## 📋 Before You Start

- You have at least one agentic workflow running in your repository (see [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)).
- You understand workflow permissions at a basic level (see [Storing Credentials with GitHub Secrets](side-quest-16-02-secrets-and-permissions.md)).

## Steps

### Protect critical files from agentic writes

The `protected-files` frontmatter key tells the agentic runner to reject any agent action that would modify the listed paths, even if `contents: write` is granted.

Open your workflow's `.md` file and add a `protected-files` block:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 9 * * 1-5"
permissions:
  contents: read
  issues: write
protected-files:
  - ".github/workflows/**"
  - "SECURITY.md"
  - "CODEOWNERS"
safe-outputs:
  create-issue: true
---
```

This ensures that even a misdirected or injected instruction cannot alter your pipeline definitions or security policy files. Compile after saving:

```bash
gh aw compile
```

> [!TIP]
> Start with `.github/workflows/**` as a minimum — it prevents the agent from modifying other agentic workflow definitions.

### Require pull-request review for permission changes

When a team member wants to increase a workflow's `permissions:` or `safe-outputs:`, that change should go through a pull request — not a direct commit to `main`.

Set up a CODEOWNERS rule for workflow files. In your repository, open or create `.github/CODEOWNERS`:

```
# Agentic workflow files require review from the workflow owners team
.github/workflows/*.md   @your-org/workflow-owners
.github/workflows/*.lock.yml   @your-org/workflow-owners
```

> [!NOTE]
> Replace `@your-org/workflow-owners` with your actual team slug. If you are working alone, add your own GitHub username (`@your-username`).

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Type `.github/CODEOWNERS` in the filename field.
4. Paste the CODEOWNERS content from above.
5. Click **Commit new file**.

</details>

### Enable required reviews on the default branch

CODEOWNERS only takes effect when branch protection requires at least one approving review.

1. In your repository on GitHub, go to **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit an existing rule for `main`).
3. Under **Require a pull request before merging**, check **Require approvals** and set the count to at least **1**.
4. Under **Require review from Code Owners**, enable that option.
5. Click **Save changes**.

Now every pull request that modifies a workflow file must be approved by a workflow owner before it merges.

### Document a workflow runbook

An unattended workflow that fails silently is an operational risk. Add a brief runbook section to the workflow file's Markdown body — just below the frontmatter — so on-call engineers know what to do.

```markdown
## Runbook

| Symptom | Likely cause | Action |
|---------|-------------|--------|
| Workflow completes but no issue created | Agent exceeded `max-ai-credits` | Check billing dashboard; raise limit or reduce context |
| `permission denied` in logs | `permissions:` scope too narrow | Open a PR to add the required scope with CODEOWNERS approval |
| Stale issue opened every day | Data source returning unchanged data | Add a deduplication step or cache-memory check |
| Run skipped | `if:` condition evaluated false | Review the conditional expression in the frontmatter |
```

> [!TIP]
> Keep the runbook in the workflow `.md` file itself — it ships with the code, stays in version history, and is visible to anyone reviewing the workflow.

## ✅ Checkpoint

- [ ] You added a `protected-files` block to at least one workflow and recompiled it
- [ ] You created or updated `.github/CODEOWNERS` to require approval for workflow file changes
- [ ] You enabled required CODEOWNERS reviews in your repository's branch protection rules
- [ ] You added a runbook table to your workflow's Markdown body

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

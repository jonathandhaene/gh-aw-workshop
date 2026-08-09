<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Shipping one workflow is a win; safely scaling agentic automation across an enterprise requires guardrails that protect every repository, team, and budget._

## 🎯 What You'll Do

You'll configure organisation-level controls so that agentic workflows run within approved boundaries. By the end you'll have a policy structure — allowed models, required permissions, and a reusable starter workflow — that any team in your organisation can adopt safely.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **organisation owner** or **enterprise admin** access, or you can talk to someone who does. Read-only reviewers can follow along and adapt for their own repositories.
- You understand what AIC budgets and `max-ai-credits` do (covered in the previous step).

## Steps

### Understand the three governance layers

Enterprise agentic governance works at three levels:

| Layer | Where it lives | What it controls |
|---|---|---|
| **Enterprise policy** | Enterprise admin → Policies | Which orgs can enable gh-aw |
| **Organisation policy** | Org → Settings → Actions | Repositories, runners, and models |
| **Workflow policy** | Frontmatter in each `.md` file | Per-run model, credits, timeout, and permissions |

You'll work at the **organisation** and **workflow** levels in this step.

### Review your organisation's Actions permissions

Open your organisation on GitHub. Navigate to **Settings** → **Actions** → **General**.

Confirm:

- **Actions permissions** allows selected or all repositories (not disabled).
- **Fork pull request workflows** is restricted — agentic workflows should not run on untrusted forks.
- **Workflow permissions** defaults to **Read repository contents and packages permissions**. The `permissions:` block in each workflow's frontmatter can reduce this further, never exceed it.

### Create a reusable starter workflow

A starter workflow lets teams create new agentic workflows with approved defaults already in place.

In your **organisation's `.github` repository**, create `.github/workflow-templates/aw-starter.md`:

```yaml
---
name: Agentic Starter (org approved)
on:
  workflow_dispatch:

permissions:
  contents: read
  issues: write

jobs:
  run:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run agentic workflow
        uses: github/gh-aw@v1
        with:
          workflow: .github/workflows/your-workflow.md
---
```

```markdown
<!-- Replace this comment with your agent instructions. -->
<!-- Keep instructions focused: one task, one outcome. -->
```

> [!TIP]
> Add `max-ai-credits` and `timeout-minutes` to the frontmatter defaults so every workflow created from this template starts within budget.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your organisation's `.github` repository on GitHub (create it if it doesn't exist).
2. Click **Add file** → **Create new file**.
3. Type `.github/workflow-templates/aw-starter.md` as the filename.
4. Paste the frontmatter and comment block above into the editor.
5. Click **Commit new file**.

</details>

### Define an allowed-models list in your documentation

GitHub does not yet enforce model allowlists at the organisation level via UI. Until that feature ships, document your approved model list in your organisation's `.github/CONTRIBUTING.md` or a `docs/agentic-workflows.md` page.

Example policy snippet:

```markdown
## Approved AI Models for Agentic Workflows

| Use case | Approved model |
|---|---|
| Code review, PR summaries | `copilot` (default) |
| Long-context analysis | `gpt-4o` |
| Cost-sensitive scheduled tasks | `gpt-4o-mini` |
```

### Set required status checks for workflow pull requests

Require that any pull request changing a `.github/workflows/*.md` file passes a compile check before merging:

1. Go to **Settings** → **Branches** → **Add branch ruleset** (or edit the existing rule for `main`).
2. Under **Require status checks to pass**, add the `gh-aw-compile` check (or whatever CI job runs `gh aw compile`).

This prevents misconfigured workflows from landing in your default branch.

> [!TIP]
> If you don't have a compile CI job yet, see [Compile and Validate Your Workflow](side-quest-07-01-compile-workflow.md) to add one.

## ✅ Checkpoint

- [ ] You reviewed your organisation's Actions permissions and confirmed fork workflow restrictions are in place
- [ ] You created (or reviewed) a reusable starter workflow template in your org's `.github` repository
- [ ] You documented an approved-models list and know where to publish it for your teams
- [ ] You set (or planned) a required status check that runs `gh aw compile` on workflow pull requests
- [ ] You can explain the three governance layers (enterprise, organisation, workflow) and what each one controls

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

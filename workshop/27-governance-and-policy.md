<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows are powerful enough that your team deserves a clear, shared agreement on how they are authored, reviewed, and approved before they run in production._

## 🎯 What You'll Do

You will draft a lightweight governance policy for agentic workflows in your organisation. By the end of this step, you will have a reusable template that captures approved models, permission ceilings, required reviewers, and a workflow promotion process — so every workflow your team ships is auditable, cost-bounded, and safe.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You understand [permissions frontmatter](https://github.github.com/gh-aw/reference/permissions/) and [safe-outputs](https://github.github.com/gh-aw/reference/safe-outputs/).
- _(Optional)_ You have read [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).

## Why governance matters for agentic workflows

A classic Actions workflow does a fixed thing each run. An agentic workflow reasons about what to do — which means its behaviour depends on the model, the brief, and live repository content. That flexibility is the point, but it creates new governance questions:

- Who approves a new agentic workflow before it runs on production repositories?
- Which models are allowed? Which are too expensive or insufficiently audited?
- What is the maximum token spend per workflow per day?
- Which write actions — comments, commits, labels, PRs — does the organisation permit?

Answering these questions once, as a team, prevents costly or unexpected surprises later.

## Draft your governance policy

A governance policy for agentic workflows typically covers five areas. Work through each one and write down your team's decision.

### Approved models and engines

List which AI engines your organisation allows. For example:

| Engine | Approved? | Notes |
|--------|-----------|-------|
| `copilot` (default) | ✅ Yes | Covered by Copilot Enterprise plan |
| `claude` (Anthropic) | ⚠️ Conditional | Requires org-approved Anthropic API key stored in a central secret |
| `codex` (OpenAI) | ⚠️ Conditional | Requires org-approved OpenAI API key |
| Other / custom | ❌ No | Must go through security review before use |

Add this table to a `docs/agentic-workflow-policy.md` file in your organisation's `.github` repository so all repositories inherit it.

### Permission ceilings

Decide the maximum `permissions:` any agentic workflow may request without an additional review step. A common starting point:

```yaml
permissions:
  contents: read       # read-only by default; write requires review
  pull-requests: write # allowed for PR comment workflows
  issues: write        # allowed for triage and labelling workflows
```

Flag any workflow that requests `contents: write` or `actions: write` for a mandatory security review before merging.

### Safe-output allowlist

Define which safe-output types are pre-approved and which need a second approver:

| Safe-output type | Pre-approved? |
|------------------|---------------|
| `add_comment` | ✅ Yes |
| `create_label` | ✅ Yes |
| `close_issue` / `close_pull_request` | ⚠️ Requires review |
| `push_to_pull_request_branch` | ⚠️ Requires review |
| `create_pull_request` with auto-merge | ❌ Requires security sign-off |

### AI Credit budget per workflow

Set a default budget and escalation path:

- **Default cap**: `max-ai-credits: 500` and `max-daily-ai-credits: 1500` for unreviewed workflows.
- **Extended budget**: workflows approved by the platform team may request up to `max-ai-credits: 2000`.
- **Alert threshold**: configure a billing alert at 80% of your monthly Copilot Enterprise budget.

### Workflow promotion process

Define how a workflow moves from development to production. A simple three-stage process works for most teams:

1. **Draft** — workflow lives on a feature branch, triggers are scoped to `workflow_dispatch` only.
2. **Review** — a team member with repository admin rights reviews the frontmatter (permissions, safe-outputs, budget caps, model) and approves the PR.
3. **Production** — workflow merges to the default branch with scheduled or event-driven triggers enabled.

> [!TIP]
> Use a required CODEOWNERS rule for `.github/workflows/*.md` so every agentic workflow change automatically requests review from your platform or security team.

## Create your policy document

Create `docs/agentic-workflow-policy.md` in your repository with the decisions from the sections above.

<details>
<summary>🖥️ GitHub UI path</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. In the filename field, type `docs/agentic-workflow-policy.md` (GitHub will create the `docs/` folder automatically).
3. Paste your policy content.
4. Click **Commit new file**.

</details>

```bash
mkdir -p docs
touch docs/agentic-workflow-policy.md
# Open the file in your editor and add your policy
git add docs/agentic-workflow-policy.md
git commit -m "Add agentic workflow governance policy"
git push
```

> [!NOTE]
> Placing this file in your organisation's `.github` repository makes it visible to all repositories in the org. Placing it in a single repository scopes it to that project only.

## ✅ Checkpoint

- [ ] You have identified at least one approved model/engine and documented why others require additional review
- [ ] You have defined a permission ceiling for agentic workflows (the maximum `permissions:` block a workflow may use without extra review)
- [ ] You have listed which safe-output types are pre-approved and which require a second approval
- [ ] You have set a default `max-ai-credits` and `max-daily-ai-credits` value for new workflows in your organisation
- [ ] You have defined a three-stage (or equivalent) workflow promotion process
- [ ] You have created `docs/agentic-workflow-policy.md` and committed it to your repository

**Next:** [Govern at Scale with the gh-aw Governance Guide](https://github.github.com/gh-aw/guides/governance/)

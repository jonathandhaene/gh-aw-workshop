<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Shipping one workflow is exciting — keeping dozens of them trustworthy across a whole organisation is what makes agentic automation sustainable._

## 🎯 What You'll Do

You will apply org-level governance controls to your agentic workflows: restrict which AI models teams may use, cap org-wide AI Credit spending, require pull-request review for workflow changes, and lock down tool access. By the end you will have a checklist of governance decisions every organisation should make before going to production.

## 📋 Before You Start

- You have at least one agentic workflow running successfully in GitHub Actions.
- You are familiar with AIC costs from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are an organisation owner or have admin access to the repository's **Settings** tab.

## Steps

### Understand the four governance levers

| Control | Where to set it | What it enforces |
|---------|-----------------|-----------------|
| Allowed models | Org **Settings → Copilot → Policies** | Only approved model families can be invoked in the org |
| Org-level AIC cap | Org **Settings → Billing → AI Credits** | Hard ceiling on total AIC consumption per month |
| Branch protection + CODEOWNERS | Repository **Settings → Branches** | Workflow `.md` changes require review before merge |
| Permitted tools | Workflow `tools:` frontmatter | Per-workflow allowlist of MCP tools the agent may call |

Start with model restriction — it is the highest-leverage single control.

### Restrict which models your org allows

1. Go to your organisation's **Settings → Copilot → Policies**.
2. Under **Allowed models for agentic workflows**, select approved model families.
3. Click **Save**.

Workflows that reference a disallowed model fail at startup with a clear error — no silent fallback.

> [!TIP]
> Start with only `copilot`. Add `claude` or `codex` only after your security team has reviewed each provider's data-handling terms.

### Set an org-wide AIC spending cap

Per-workflow caps (set in frontmatter) protect individual workflows. An org-level cap is your safety net for the total bill.

1. Go to your organisation's **Settings → Billing → AI Credits**.
2. Under **Monthly spending limit**, enter your desired ceiling in AIC.
3. Click **Save spending limit**.

When the org cap is reached, all agentic workflow runs in the organisation pause until the next billing cycle. Communicate this boundary to your teams so they are not surprised.

### Require code review for workflow changes

Workflow `.md` files are code. Add a `CODEOWNERS` entry:

```text
# .github/CODEOWNERS
/.github/workflows/*.md   @your-org/workflow-reviewers
```

Then enable branch protection on your default branch:

1. Go to **Settings → Branches → Add branch protection rule**.
2. Apply the rule to `main`.
3. Enable **Require a pull request before merging** and **Require review from Code Owners**.
4. Click **Create**.

<details>
<summary>Why this matters for agentic workflows specifically</summary>

A workflow's **Markdown body is its task brief** — prose that encodes the agent's intent at runtime. A small sentence edit can significantly change what the agent does. Human review is your last line of defence before it runs unattended on a schedule.

</details>

### Lock down the `tools:` allowlist in each workflow

Each workflow should declare exactly the MCP tools it needs, nothing more.

Open your workflow `.md` file and verify the `tools:` section is explicit:

```yaml
---
tools:
  - github
# no wildcard, no extra servers
---
```

Remove any tools that are not actually called in the task brief. A workflow that reads pull requests does not need write access to projects or discussions. Keeping the `tools:` list minimal limits the blast radius if the agent is ever misdirected.

After editing, compile and commit:

```bash
gh aw compile
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow `.md` file in the repository.
2. Click the **pencil icon (✏️)** to edit it.
3. Remove unused tool entries from the `tools:` section.
4. Click **Commit changes** and open a pull request for review.

The compiled `.lock.yml` will be regenerated automatically when the workflow next runs. You can also trigger a manual workflow run to force regeneration.

</details>

### Document your governance decisions

Record your governance choices somewhere your team can find them — a Markdown file, a GitHub issue, or a team wiki page:

- Which models are permitted and why
- What the org-level AIC cap is and who owns raising it
- Which team reviews workflow changes
- Where to escalate if a workflow behaves unexpectedly

This document becomes your audit trail for compliance reviews and onboarding new workflow authors.

## ✅ Checkpoint

- [ ] You can locate the **Allowed models** policy in your org's Copilot settings (or know who to ask)
- [ ] You can describe the difference between a per-workflow AIC cap and an org-level AIC cap
- [ ] A `CODEOWNERS` entry covers your workflows directory and branch protection requires code-owner review
- [ ] Each of your workflow `.md` files has an explicit, minimal `tools:` allowlist
- [ ] You have documented (or could document) your organisation's key governance decisions

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

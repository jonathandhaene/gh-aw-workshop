<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Production-grade agentic workflows need guardrails — governing who can publish them and what they're allowed to do is what turns a personal tool into a trusted platform._

## 🎯 What You'll Do

You'll review the governance controls available for agentic workflows: branch protection, permission scoping, task brief review, and organisation-level policy settings. You'll leave with a checklist your team can use before promoting any agentic workflow to a production schedule.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow has run at least once and you have reviewed its output.

## Steps

### Understand the governance model

Agentic workflows run as GitHub Actions jobs, so standard GitHub governance controls apply — plus a few agentic-specific ones.

Three layers to think about:

1. **Repository controls** — who can push workflow changes and who must review them.
2. **Workflow frontmatter** — what permissions the agent requests and what it can write.
3. **Organisation policy** — which models are permitted and whether AIC budgets are enforced centrally.

> [!NOTE]
> On GHES and GHEC, organisation owners can restrict which Copilot models are available to Actions workflows. Confirm with your administrator before depending on a specific model.

### Protect the workflow file with branch rules

Any file under `.github/workflows/` should require a pull-request review before merging — the same as production application code. Without this, any contributor with write access can change what your workflow does and what it posts.

To add a branch rule in the GitHub UI:

1. Go to your repository on GitHub and click **Settings** → **Branches**.
2. Click **Add branch ruleset**.
3. Set the target branch to `main`.
4. Enable **Require a pull request before merging** with at least one required reviewer.
5. Click **Save changes**.

> [!TIP]
> If your organisation uses a shared `.github` repository for rulesets, add the rule there so it applies automatically to every new repository.

### Scope permissions tightly in frontmatter

Keep the permissions block to the minimum the workflow actually needs. A read-and-report workflow needs no write permissions beyond what its safe-output declares. Reviewers should challenge any permission that cannot be justified by the task brief.

> [!NOTE]
> On GHEC and GHES, organisation policy may cap the maximum permission level for Actions workflows. A workflow requesting broader permissions than the policy allows will fail at runtime.

### Review the task brief in code review

When reviewing a PR that changes a workflow's `.md` file, check the brief as carefully as the frontmatter. Flag:

- Instructions that require broader permissions than declared.
- Open-ended phrases like "do whatever you think is best".
- Instructions that reference external systems not covered by declared tools.

### Enable organisation-level policy settings

1. Go to **Organisation settings** on GitHub and click **Copilot** → **Policies**.
2. Disable any model your team is not prepared to use or pay for.
3. Confirm per-repository AIC limits in workflow frontmatter align with any organisation ceiling.

## ✅ Checkpoint

- [ ] Your workflow's `.github/workflows/` path is protected by a branch ruleset requiring PR review
- [ ] The permissions block in your workflow frontmatter lists only what the workflow actually needs
- [ ] You reviewed the task brief for open-ended instructions and tightened any you found
- [ ] You checked your organisation's Copilot policy settings and confirmed the model your workflow uses is permitted
- [ ] _(Enterprise users)_ Your per-repository AIC limits align with any organisation-level ceiling

<!-- journey: all -->
Want to explore more workshop paths? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

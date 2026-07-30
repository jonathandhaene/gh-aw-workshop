<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Building one workflow is a proof of concept — governing a library of them across your organisation is what makes agentic automation sustainable._

## 🎯 What You'll Do

You will map out the governance practices your team needs to adopt agentic workflows safely and consistently. By the end of this step, you will have a lightweight governance plan covering workflow ownership, permission policy, cost controls, and a review process for new workflows.

## 📋 Before You Start

- You have built and run at least one agentic workflow (see [Run Your Workflow](08-run-your-workflow.md)).
- You have reviewed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have read [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).

> [!NOTE]
> This step is most relevant to team leads, platform engineers, and DevOps practitioners adopting agentic workflows across multiple teams or repositories. Solo learners can skim this step and return when they are ready to share their workflows with colleagues.

## Understand what changes at scale

When one person authors one workflow, trust is simple — you own everything. When multiple authors create workflows that run on shared runners, access shared secrets, and consume organisation AI credits, governance becomes a first-class concern.

Three areas need explicit policy:

1. **Permissions** — which workflows can read or write which resources
2. **AI credit budgets** — who sets spending caps and how overruns are escalated
3. **Workflow review** — who approves a new agentic workflow before it runs in production

## Define a permissions policy

The least-privilege principle applies to every workflow. Work with your team to answer these questions:

- Which repositories should allow `contents: write` in agentic workflows? (Consider restricting this to dedicated bot or automation repositories.)
- Which secrets can workflows access? Create a naming convention (for example, `AW_` prefix) so it is easy to identify workflow-specific secrets at a glance.
- Should all workflows use `GITHUB_TOKEN`, or do some require a personal or app token? Document when each is appropriate.

Write your answers down as a short decision record — even a comment at the top of a shared workflow template is enough to start.

## Set organisation-wide cost controls

GitHub's billing dashboard shows AI credit usage per organisation. Work with your administrator to:

1. Enable billing alerts so your team is notified before spending exceeds a threshold.
2. Agree on a default `max-ai-credits` value that every new workflow inherits from your template (see [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md)).
3. Decide who can override the default limit and how overrides are approved.

A simple policy: new workflows start with a conservative limit (for example, 100 AIC per run) and can raise it with team lead approval after the first month's usage data is available.

## Create a lightweight review process

Before any agentic workflow goes live in a shared repository, a second set of eyes helps catch:

- Overly broad `permissions:` blocks
- Missing `safe-outputs` declarations that could allow unintended writes
- Briefs that are vague enough to produce unpredictable output

A pull-request checklist captures this. Add a `.github/pull_request_template.md` to your repository (or organisation `.github` repository) with a section for agentic workflow reviews:

```markdown
## Agentic Workflow Review (complete if this PR adds or changes a workflow)

- [ ] `permissions:` uses only the scopes this workflow needs
- [ ] `safe-outputs:` explicitly lists every write the agent may perform
- [ ] `max-ai-credits:` is set and approved by the team lead
- [ ] The agent brief includes a clear termination condition
- [ ] `gh aw compile` passes with no errors
- [ ] A test run has been reviewed in the Actions log
```

<details>
<summary>🖥️ Add the checklist via the GitHub UI</summary>

1. Open your repository on GitHub and click **Add file** → **Create new file**.
2. In the filename field, type `.github/pull_request_template.md`.
3. Paste the checklist content above.
4. Click **Commit new file**.

Future pull requests in this repository will automatically include this checklist.

</details>

## Designate workflow owners

Every production agentic workflow should have a named owner (a person or team) responsible for:

- Monitoring run outcomes and costs each week
- Updating the workflow when the underlying data sources or agent APIs change
- Triaging failures flagged by the audit report (see [Audit and Monitor](25-audit-and-observability.md))

Add an `# Owner: @team-handle` comment at the top of each workflow's Markdown body. It takes ten seconds and prevents "nobody knows who runs this" six months later.

## ✅ Checkpoint

- [ ] You have documented at least one rule for each governance area: permissions, cost controls, and workflow review
- [ ] Your team has agreed on a default `max-ai-credits` value for new workflows
- [ ] You have created or planned a pull request checklist for agentic workflow reviews
- [ ] Every production workflow in your repository has a named owner

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

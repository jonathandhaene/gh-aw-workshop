<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _A single workflow is powerful; dozens of them running unsupervised across an organisation are risky without clear policy — this step shows you how to stay in control._

## 🎯 What You'll Do

You'll learn the controls available in GitHub to restrict which workflows can run, who can approve new ones, and how to apply organisation-wide guardrails. By the end, you'll have a repeatable governance checklist you can share with your team or security reviewers.

## 📋 Before You Start

- You've completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or are familiar with `max-ai-credits` and `max-daily-ai-credits`.
- You have admin access to a GitHub organisation, or you can read along and apply the steps in a personal repository.

## Steps

### Understand what you're governing

Agentic workflows can read repository data, post comments, open pull requests, and call external services. Governance means answering three questions for every workflow in your organisation:

1. **Who authored it?** Is the source reviewed and trusted?
2. **What can it write?** Are `safe-outputs` and `permissions:` scoped to the minimum needed?
3. **How often and how much can it spend?** Are cost caps in place?

### Review organisation-level Actions settings

GitHub lets you limit which Actions (and by extension, which agentic workflows) can run.

1. Go to your organisation on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Policies**, choose one of:
   - **Allow all actions** — widest; fine for small, trusted teams.
   - **Allow select actions** — restrict to actions you've vetted; recommended for enterprise.
   - **Disable Actions** — blocks everything; use only when a full freeze is needed.
4. Scroll to **Workflow permissions**. Set the default `GITHUB_TOKEN` permission to **Read repository contents and packages** unless a workflow explicitly requires write access.

> [!TIP]
> Setting a read-only default means every workflow that needs to write must declare `permissions: write-all` or a specific write scope in its frontmatter — making write access visible and auditable.

![Organisation Actions settings showing the Workflow permissions section](images/27-org-actions-settings.svg)

### Require pull request reviews for workflow changes

Agentic workflow definitions live in `.github/workflows/*.md`. Treat them like production code.

1. In your repository, go to **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit your existing `main` ruleset).
3. Enable **Require a pull request before merging**.
4. Enable **Require approvals** and set the count to at least **1** (or **2** for high-sensitivity repositories).
5. Optionally, add a [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) file entry so workflow files always route to a designated reviewer:

```text
# .github/CODEOWNERS
.github/workflows/   @your-org/workflow-reviewers
```

### Apply a standard frontmatter policy

Agree on a baseline set of frontmatter fields that every agentic workflow in your organisation must include. Document this in your team's contributing guide or AGENTS.md:

```yaml
---
name: <Descriptive workflow name>
on: <trigger>
timeout-minutes: 10          # always set a timeout
max-ai-credits: 500          # per-run cap
max-daily-ai-credits: 2000   # 24-hour ceiling
permissions:
  contents: read             # minimum; add write only when needed
safe-outputs:
  <tool>:
    limit: <N>               # explicit write budget per tool
---
```

> [!NOTE]
> `timeout-minutes` and `max-ai-credits` together create a two-layer safety net: one stops runaway wall-clock time, the other stops runaway token spend. Both should be mandatory in your policy.

### Audit existing workflows

Before rolling out a policy, audit what's already running.

1. In your repository, go to **Actions** → select a workflow → **Usage**.
2. Check: Does every run have a cost cap? Does the token scope match what the workflow actually needs?
3. For a bulk audit across an organisation, ask your platform or security team to query the organisation's [audit log](https://github.github.com/gh-aw/reference/audit/) for `workflow_run` events.

<details>
<summary>🏢 GHES and GHEC notes</summary>

**GitHub Enterprise Server (GHES):**

- Actions policies are configured under **Enterprise settings** → **Policies** → **Actions** in addition to the organisation level. Enterprise policies override organisation settings.
- The audit log is accessible at **Enterprise** → **Audit log** and can be streamed to a SIEM via [audit log streaming](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise).
- Self-hosted runner groups (configured at the enterprise level) let you restrict which organisations and repositories can use which runners — an important control when runners have elevated network access.

**GitHub Enterprise Cloud (GHEC):**

- Use [enterprise-level branch protection rulesets](https://docs.github.com/en/enterprise-cloud@latest/admin/policies/enforcing-policies-for-your-enterprise/about-enterprise-policies) to enforce PR reviews on workflow files across all repositories in the enterprise.
- If your organisation uses SAML SSO, ensure the PAT or token used by any external MCP server is SSO-authorised; an unauthorised token will be silently blocked at runtime.

</details>

### Communicate the policy to your team

Governance only works when everyone knows the rules. Add a short section to your repository's AGENTS.md or CONTRIBUTING.md:

- Link to your organisation's approved workflow templates.
- State the required frontmatter fields.
- Describe the review process for new workflows.
- Include a pointer to the [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/).

## ✅ Checkpoint

- [ ] You located your organisation's Actions **Policies** and **Workflow permissions** settings
- [ ] You set (or confirmed) the default `GITHUB_TOKEN` permission to **Read repository contents**
- [ ] You have a branch ruleset or protection rule requiring PR review for `.github/workflows/` changes
- [ ] You added or confirmed a `CODEOWNERS` entry routing workflow files to a reviewer team
- [ ] Your team has a documented standard for required frontmatter fields (timeout, cost caps, minimal permissions)
- [ ] You know where to find the organisation audit log and what event type to filter on
- [ ] You can describe at least one difference in governance controls between github.com, GHEC, and GHES

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

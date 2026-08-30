<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows can act on your behalf at scale — the policies you put in place now protect your team, your code, and your users later._

## 🎯 What You'll Do

Set up an organisational governance layer for agentic workflows: configure org-level policy settings, add required reviewers to sensitive workflows, and document an approval process so your team knows what needs a human sign-off before shipping an agentic workflow.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or any advanced node in the [What's Next?](14-next-steps.md) hub.
- You have admin access to your GitHub organisation (or a test organisation you can experiment with).
- If you are on GHES, confirm you are running **GHES 3.12 or later** — see [Enterprise Setup Considerations](side-quest-enterprise-setup.md).

## Steps

### Understand what governance covers

Governance for agentic workflows has three layers:

1. **Organisation policy** — which repositories and actors can create or run agentic workflows at all.
2. **Workflow-level controls** — `permissions:`, `safe-outputs`, and `network.allowed-domains` in each workflow file.
3. **Review and approval** — human gates before a new or updated workflow goes live.

You already know layer 2 well from earlier steps. This node focuses on layers 1 and 3.

### Review organisation-level Copilot settings

Agentic workflows run via the Copilot cloud agent. An organisation admin controls which repositories can use it.

1. In your organisation on GitHub, go to **Settings** → **Copilot** → **Policies**.
2. Under **GitHub Copilot in GitHub Actions**, choose one of:
   - **Allowed for all repositories** — any repo in the org can run agentic workflows.
   - **Allowed for selected repositories** — only repos you explicitly list.
   - **Disabled** — no agentic workflow runs anywhere in the org.

> [!TIP]
> Start with **selected repositories** during rollout. Add repositories as teams demonstrate responsible use.

Record your decision in your team's runbook or internal wiki — "we allow agentic workflows in repos tagged `agentic-approved`" is a concrete, auditable policy.

### Require pull-request review for workflow changes

Every agentic workflow lives in `.github/workflows/` as a `.md` file. Treating this directory like production infrastructure is the right instinct: require a human review before any change merges.

1. Go to your repository's **Settings** → **Branches**.
2. Add or edit a branch protection rule for your default branch (`main`).
3. Enable **Require a pull request before merging**.
4. Set **Required approvals** to at least `1` (use `2` for high-impact workflows that write to issues, PRs, or external systems).
5. Optionally, add a **CODEOWNERS** entry so the right team is auto-requested:

```text
# .github/CODEOWNERS
.github/workflows/   @my-org/platform-team
```

This means every new or updated agentic workflow gets reviewed by `platform-team` before it can merge.

### Define an approval checklist

Paste this checklist into your pull-request template (`.github/pull_request_template.md`) or document it in your wiki. Reviewers should confirm each item before approving a new agentic workflow:

```markdown
## Agentic Workflow Review Checklist

- [ ] `permissions:` uses only the minimum scopes required
- [ ] `safe-outputs` lists every GitHub write action the agent may take
- [ ] `network.allowed-domains` is set when the workflow calls external URLs
- [ ] `max-ai-credits` and `max-daily-ai-credits` are set to reasonable limits
- [ ] The task brief cannot be trivially misled by repository content (prompt-injection risk reviewed)
- [ ] A manual `workflow_dispatch` test run was completed in a non-production repository
```

### Document your governance decision log

Create a `docs/agentic-workflows-policy.md` file in your repository (or your org's `.github` repository so it applies globally). Capture:

- Which workflow files are approved to run in production.
- Who owns each workflow (use GitHub usernames or team slugs).
- The date each workflow was last reviewed.

A simple table works well:

```markdown
| Workflow file | Owner | Last reviewed | Status |
|---|---|---|---|
| daily-status.md | @platform-team | 2025-06-01 | ✅ Approved |
| pr-reviewer.md | @platform-team | 2025-06-15 | ✅ Approved |
```

> [!NOTE]
> For GHEC organisations, you can also enforce these controls via **organisation rulesets** (Settings → Rules → Rulesets), which let you target multiple repositories at once with a single branch protection configuration.

## ✅ Checkpoint

- [ ] You located the Copilot policy setting in your organisation and confirmed which repositories are allowed to run agentic workflows
- [ ] You enabled (or confirmed the existence of) a branch protection rule requiring at least one pull-request approval for changes to `.github/workflows/`
- [ ] You created or updated a `CODEOWNERS` entry routing `.github/workflows/` reviews to a specific team or person
- [ ] You saved the agentic workflow review checklist somewhere your team will find it (PR template, wiki, or `docs/`)
- [ ] You created a governance decision log entry for at least one workflow

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

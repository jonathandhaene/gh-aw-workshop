<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _One workflow running in one repo is a great start — but when your team ships dozens of them, you need policy, visibility, and guardrails that scale._

## 🎯 What You'll Do

Set up an organisation-level governance posture for agentic workflows: define reusable policy templates, enforce permission boundaries across repos, and give your security team the audit trail they need to stay compliant.

## 📋 Before You Start

- You have at least one working agentic workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have admin access to a GitHub organisation (or can ask an admin to apply the settings alongside you).

## Steps

### Understand what governance means for agentic workflows

An agentic workflow can read repository data, call external services, and write back to GitHub. Those capabilities are powerful — and they mean every workflow is a small automated agent acting on behalf of your team.

Governance answers three questions:

1. **Who can create a workflow?** (authoring policy)
2. **What can a workflow do?** (permission policy)
3. **What happened in every run?** (audit policy)

You will configure each area in the steps below.

### Set an organisation-wide `permissions:` baseline

The most effective guardrail is a minimal default. In each workflow's frontmatter, keep permissions to the lowest scope the workflow needs:

```yaml
---
name: Daily Status Report
permissions:
  contents: read
  issues: write
---
```

For organisation-wide enforcement, create a [reusable called workflow](https://github.github.com/gh-aw/guides/reusing-workflows/) that other workflow authors can import. Wrap your shared permission policy in that called workflow's frontmatter so no consumer can accidentally widen the scope.

> [!TIP]
> If your organisation is on GitHub Enterprise Cloud (GHEC), you can enforce required workflow templates at the org level using **Repository rulesets**. Reach out to your org admin to apply a ruleset that requires all agentic workflows to call a shared policy workflow before running.

### Lock down network access with `allowed-domains`

Limit which external hosts a workflow can reach. Without this, a misdirected agent could attempt to call any URL it infers from repository content.

Add `network.allowed-domains` to every workflow that calls external data:

```yaml
---
name: Daily Status Report
network:
  allowed-domains:
    - api.github.com
    - github.com
---
```

Keep the list short and specific. Remove any domain the workflow does not actually need. Review the list when you update the workflow's task brief.

<details>
<summary>🏢 GitHub Enterprise Server (GHES) note</summary>

On GHES, your organisation may run behind a proxy or firewall. Replace `api.github.com` with your GHES hostname (for example, `github.example.com`) and add any internal API hosts your workflow needs to reach. Work with your network team to confirm the runner's outbound allowlist matches what you declare here.

</details>

### Protect critical files from agentic writes

Use `protected-files` to prevent the agent from overwriting sensitive paths — even if the task brief is manipulated:

```yaml
---
name: Daily Status Report
protected-files:
  - .github/workflows/**
  - CODEOWNERS
  - SECURITY.md
---
```

List any file that, if changed unexpectedly by an agent, would create a security or compliance risk. Governance configuration files, branch protection rules, and licence files are common choices.

### Route audit artifacts to a central repository

Every agentic workflow run produces an audit artifact. For compliance, consolidate those artifacts into a shared repository that your security team can access.

In your workflow frontmatter, set `audit-output` to write the artifact to a path that a separate data-collection workflow can pick up:

```yaml
---
name: Daily Status Report
audit-output: audit/daily-status/${{ github.run_id }}.json
---
```

Then create a companion workflow in a central `security-hub` repository that reads those artifacts on a schedule and posts a governance summary. See [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) for the artifact format details.

### Create a shared workflow template catalogue

Instead of letting each team author workflows from scratch, publish approved templates to a shared repository. Team members install them with:

```bash
gh aw add owner/security-hub/templates/daily-status-template
```

The template already contains the correct `permissions:`, `network.allowed-domains`, and `protected-files` for your organisation. Authors customise only the task brief. See [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md) for how to publish a template.

> [!NOTE]
> Shared templates are the fastest way to spread best practice. Once the security team approves a template, every team that installs it automatically inherits the policy settings — no individual review required.

### Review and communicate your governance policy

Write a one-page policy document (a GitHub wiki page or a `AGENTIC-WORKFLOW-POLICY.md` file in your `.github` repository) that covers:

- Which permission scopes are allowed by default
- Which external domains are approved for `allowed-domains`
- How to request a new domain or permission scope
- How long audit artifacts are retained
- Who reviews compliance reports

Link to this policy from your shared workflow template's README so every author can find it.

## ✅ Checkpoint

- [ ] Every workflow in your practice repository has a `permissions:` block with the minimum required scopes
- [ ] You added `network.allowed-domains` to at least one workflow and removed any hosts it does not use
- [ ] You added `protected-files` entries covering your organisation's critical file paths
- [ ] You can describe the difference between authoring policy, permission policy, and audit policy in your own words
- [ ] You know where audit artifacts are stored and how your security team can access them
- [ ] You drafted or identified where you would put your organisation's agentic workflow governance policy document

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

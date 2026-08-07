# Govern Your Agentic Workflows at Scale

> _Shipping one workflow is exciting — but in a team or enterprise, governance turns individual experiments into trustworthy, auditable automation._

## 🎯 What You'll Do

You'll learn how to apply organizational controls to your agentic workflows: required workflows, permission policies, and GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) settings that keep AI-powered automation safe and consistent across teams.

## 📋 Before You Start

- You've completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one working agentic workflow in your repository.
- If you are on GitHub Enterprise Server (GHES) or GitHub Enterprise Cloud (GHEC), your organization admin can help with the settings referenced below — bookmark this page for that conversation.

## Understand the Three Governance Layers

Governance for agentic workflows operates at three levels: the **repository** (you control this), the **organization** (an admin or team lead controls this), and the **enterprise** (a platform team controls this).

| Layer | Who controls it | Key controls |
|-------|----------------|-------------|
| Repository | You | `permissions:`, `safe-outputs:`, `network.allowed-domains:`, `max-ai-credits:` |
| Organization | Org admin | Required workflows, workflow approval rules, Copilot billing policy |
| Enterprise | Enterprise admin | Actions enablement policy, runner groups, GHES-level Copilot access |

As a workflow author, you directly control the repository layer. The sections below help you design workflows that fit cleanly into the organization and enterprise layers too.

### Write least-privilege workflows by default

Every workflow you author should declare only the permissions it genuinely needs. This is both good security hygiene and a requirement in many enterprise governance frameworks.

Prefer this pattern:

```yaml
permissions:
  contents: read
  pull-requests: write
```

Over this:

```yaml
permissions:
  contents: write
  pull-requests: write
  issues: write
  actions: write
```

Adding `write` access you don't use is the single most common governance finding in agentic workflow reviews.

### Use `safe-outputs` as your write allowlist

The `safe-outputs:` block in your workflow frontmatter acts as an explicit declaration of what the agent is allowed to write. Reviewers — both humans and automated policy checks — can read this block to understand the blast radius of a run before it executes.

Keep the list narrow. If your workflow currently declares more write actions than it actually uses, remove the unused entries.

### Understand required workflows (GHEC/GHES)

On GHEC and GHES, organization admins can configure **required workflows**: workflows in a central repository that run automatically on every repository in the organization. These are standard GitHub Actions workflows, not agentic workflows — but they gate whether other workflows (including yours) can complete.

If a required workflow fails in your organization, your agentic workflow run may be blocked. Speak to your org admin about:

- Which repositories have required workflows enabled.
- What those required workflows check (security scans, policy lints, approval gates).
- Whether your workflow frontmatter structure satisfies any machine-readable policy checks.

> [!NOTE]
> Required workflows are configured at the organization level under **Settings → Actions → Required workflows**. You will need org admin access to create or modify them — but as a workflow author you should know they exist and what they check.

### Copilot billing policy for Actions (GHEC/GHES)

On GHEC and GHES, Copilot access for GitHub Actions is controlled by the organization's Copilot policy. If your agentic workflow uses Copilot as its engine and the policy is not enabled, runs will fail at the model request step.

Check with your org admin whether **Copilot in GitHub Actions** (sometimes called centralized billing for Actions) is enabled. The `side-quest-06-03a-copilot-requests-permission.md` side quest walks through this in detail for learners on centralized billing.

<details>
<summary>🏢 For GitHub Enterprise Server (GHES) administrators</summary>

On GHES, agentic workflow support depends on the version of your GHES instance and your Copilot for Business or Copilot Enterprise license. Check:

1. Your GHES version supports the `gh-aw` runner extension (contact your GitHub account team for compatibility).
2. Copilot access is enabled at the enterprise level under **Enterprise settings → Copilot → Policies**.
3. Runner groups are configured to allow the repositories that need agentic workflow access.
4. The `network.allowed-domains:` block in each workflow is reviewed against your corporate proxy or egress firewall rules.

</details>

### Document your governance decisions in the workflow file

The Markdown body of your workflow file is the right place to explain governance-relevant design decisions — not just the task brief. Add a short comment block at the top of the body explaining what the workflow writes, why those permissions are needed, and who to contact if the workflow needs to change.

```markdown
<!-- Governance note:
  This workflow reads pull request metadata (contents: read, pull-requests: write)
  and posts a review comment. It does not commit code or create branches.
  Owner: platform-team@example.com
-->
```

This makes your workflow self-documenting for the next reviewer — human or AI.

## ✅ Checkpoint

- [ ] You can describe the three governance layers (repository, organization, enterprise) and which one you control directly.
- [ ] Your workflow's `permissions:` block lists only the scopes the workflow actually uses.
- [ ] Your workflow's `safe-outputs:` block contains only write actions that the workflow performs.
- [ ] You know whether your organization uses required workflows and what they check.
- [ ] You know whether Copilot in GitHub Actions is enabled for your organization.
- [ ] You've added or reviewed a governance comment block in at least one workflow's Markdown body.

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

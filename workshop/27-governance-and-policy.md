<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Individual cost controls are a great start — but sustainable AI automation also needs organisation-level policies that let administrators oversee, approve, and scope what agentic workflows can do._

## 🎯 What You'll Do

You'll explore the GitHub settings that let organisation admins govern agentic workflows: Copilot policy toggles, permitted tool scopes, and workflow approval requirements. By the end you'll know which settings to review with your administrator before rolling out workflows at scale.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a GitHub organisation where you are either an **owner** or can request changes from one.
- Enterprise learners: confirm your GHES instance is 3.12 or later (see [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md)).

## Steps

### Understand the governance layers

Agentic workflow governance works at three levels:

| Layer | Who controls it | Where to configure |
|---|---|---|
| **Enterprise** | Enterprise admin | Enterprise settings → Policies |
| **Organisation** | Org owner | Organisation settings → Copilot |
| **Repository / workflow** | Repository admin / workflow author | Workflow frontmatter |

Each lower layer can only be _as permissive_ as the layer above it. If your enterprise disables a Copilot feature, no organisation policy can re-enable it.

### Review Copilot policy settings for your organisation

1. Navigate to your organisation on GitHub.
2. Click **Settings** → **Copilot** in the left sidebar.
3. Under **Policies**, review two key controls:
   - **Copilot coding agent** — enables or disables the cloud agent that powers agentic workflows.
   - **Suggestions matching public code** — determines whether Copilot output may match public code snippets. Review this against your IP policy before deploying workflows that generate code.
4. Save any changes if you are an org owner.

> [!NOTE]
> On GitHub Enterprise Cloud, policies can be enforced at the enterprise level, preventing org owners from overriding them. If the toggles are greyed out, check with your enterprise admin.

### Check Actions permissions and workflow approval settings

Agentic workflows compile to standard GitHub Actions YAML (`.lock.yml` files). That means your organisation's **Actions permissions** apply directly.

1. In your organisation settings, click **Actions** → **General**.
2. Under **Actions permissions**, verify that **Allow all actions and reusable workflows** or a scoped equivalent is selected — restrictive settings block agentic workflows from running.
3. Under **Fork pull request workflows**, confirm that contributors outside your org cannot trigger agentic workflows on pull requests from forks unless you intend that.
4. Under **Required approvals**, consider requiring a reviewer to approve workflow runs from first-time contributors. This is a low-friction safeguard against untested workflows consuming AI Credits or making unintended writes.

![GitHub Actions General settings showing workflow approval and permissions controls](images/27-actions-general-settings.svg)

### Scope permissions in the workflow frontmatter

Each agentic workflow can declare only the permissions it needs. Tight scoping reduces blast radius if a workflow behaves unexpectedly.

```yaml
---
name: PR Reviewer
on:
  pull_request: {}
permissions:
  contents: read
  pull-requests: write
---
```

The `permissions` block follows standard [GitHub Actions permissions syntax](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-can-do/assigning-permissions-to-jobs). Omitting it gives the workflow your organisation's **default token permissions** — usually `read` for most scopes. Granting only what is needed is good hygiene and a requirement in many enterprise security policies.

> [!TIP]
> For a full list of scopes available in agentic workflow frontmatter — and how they interact with the Copilot cloud agent's own token — see [Frontmatter Reference: permissions](https://github.github.com/gh-aw/reference/frontmatter/#permissions).

### Document your governance decisions

Before sharing agentic workflows with your team, record three things in a shared doc or wiki:

1. **Which Copilot policies are enabled** and who approved them.
2. **Permitted AI Credit budgets** per workflow and per team (link to [Step 26](26-manage-costs-and-budgets.md)).
3. **Workflow approval requirements** — which repositories require a human review before a compiled `.lock.yml` is merged.

This lightweight record keeps audits straightforward and gives new contributors a clear starting point.

> [!TIP]
> If your organisation uses the [Actions governance guide](https://github.github.com/gh-aw/guides/governance/), it maps these decisions to a recommended review cadence.

## ✅ Checkpoint

- [ ] You reviewed the **Copilot** policy settings for your organisation and confirmed the coding agent is enabled (or know who to ask)
- [ ] You reviewed the **Actions permissions** settings and confirmed agentic workflow `.lock.yml` files can run
- [ ] You added or verified a `permissions` block in at least one of your workflow frontmatter files
- [ ] You documented (or noted) the three governance decisions: policies, budgets, and approval requirements

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

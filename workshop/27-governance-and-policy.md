<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organization

> _When one workflow is powerful, ten across your org can be transformative — but only if you have visibility, guardrails, and a repeatable process for keeping them safe and cost-effective._

## 🎯 What You'll Do

You'll review the governance options available to organization and enterprise administrators — including org-level policy controls, required workflow reviews, spending limits, and audit log access — and apply at least one control to your own repository to demonstrate the principle of least privilege at scale.

## 📋 Before You Start

- You've completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have an agentic workflow running in a repository that belongs to a GitHub organization (personal repos work for most steps; org-level controls require organization admin access).

## Steps

### Understand the governance layers

Agentic workflows inherit the same governance model as standard GitHub Actions, with one extra layer: AI model access and AIC spending.

There are three places where policy lives:

| Layer | Who controls it | What it protects |
|-------|----------------|-----------------|
| **Workflow frontmatter** | Workflow author | Permissions, safe outputs, cost caps per workflow |
| **Repository settings** | Repository admin | Which Actions are allowed, required status checks, branch protection |
| **Organization / enterprise settings** | Org or enterprise admin | Org-wide Actions policy, Copilot for Actions, spending limits, audit log |

Each layer is independently enforceable. A workflow author can set `max-ai-credits: 500` even if the org has a higher limit — the stricter value wins.

### Review organization-level Actions policies

Organization admins can restrict which Actions (including agentic workflows) are allowed to run.

1. Navigate to your organization on GitHub.
2. Click **Settings** → **Actions** → **General**.
3. Under **Actions permissions**, choose one of:
   - **Allow all actions** — any action or workflow can run.
   - **Allow select actions** — restrict to verified creators or specific action names.
4. Review the **Fork pull request workflows** setting to control whether forks can trigger workflows with write access.

> [!NOTE]
> For GHES environments, these same settings appear under **Admin** → **Policies** → **Actions** at the instance level, and can be set globally or per organization.

### Apply a required reviewer to workflow changes

Protecting your `.github/workflows/` directory with a [code owner rule](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) ensures that every change to an agentic workflow file is reviewed before it ships.

1. In your repository, create or open `.github/CODEOWNERS`.
2. Add a rule that assigns reviewers to the workflows directory:

```
# Require review for all agentic workflow files
.github/workflows/   @your-org/platform-team
```

1. Commit and push the file.
1. Enable **Require a pull request before merging** on your default branch under **Settings** → **Branches**.

Any change to a `.md` or `.lock.yml` file in `.github/workflows/` now requires approval from your platform team before merging.

<details>
<summary>🖥️ GitHub UI alternative for editing CODEOWNERS</summary>

1. In your repository, click **Add file** → **Create new file**.
2. Type `.github/CODEOWNERS` as the file path.
3. Add the ownership rule for your workflows directory.
4. Click **Commit new file**.

</details>

### Set an organization-wide spending limit

If your organization has GitHub Copilot Enterprise or Copilot for Business, you can set a monthly AIC spending cap at the org level.

1. Go to your organization **Settings** → **Billing** → **Plans and usage**.
2. Locate the **Copilot** section and click **Manage spending limit**.
3. Set a monthly AIC ceiling that gives your teams room to experiment without runaway costs.

> [!TIP]
> Start with twice your measured baseline from [Step 26](26-manage-costs-and-budgets.md). Review the limit after 30 days once you have real usage data.

### Check the org-level audit log for agentic workflow events

The GitHub audit log records who created, edited, or triggered workflows across your organization.

1. Go to **Settings** → **Audit log** in your organization.
2. Filter by the `workflows` category to see workflow trigger and permission change events.
3. Filter by `copilot` to see AI model usage events (available for Copilot Enterprise plans).

> [!NOTE]
> `gh aw audit` (covered in [Step 25](25-audit-and-observability.md)) gives per-workflow run detail. The GitHub org audit log gives cross-workflow, cross-repository history — combine both for a complete picture.

### Define a minimal-permission standard for your team

Governance is most effective when it is baked into the authoring process, not bolted on afterward. Define a team standard for new workflows:

```yaml
---
permissions:
  contents: read      # default: only read; escalate to write only when needed
  issues: read        # add write only for workflows that must open/close issues
# Never grant: admin, secrets, or organization-level scopes
---
```

Share this template with your team as a starting point for every new workflow. Pair it with the CODEOWNERS rule so reviewers can check that new workflows don't over-scope permissions.

## ✅ Checkpoint

- [ ] You can name the three governance layers (workflow, repository, organization) and explain what each protects
- [ ] You reviewed your organization's Actions permissions policy (or your repository settings if using a personal account)
- [ ] You added or reviewed a `.github/CODEOWNERS` entry that covers `.github/workflows/`
- [ ] You checked (or noted where to find) your organization's Copilot spending limit
- [ ] You reviewed the organization audit log and identified at least one workflow-related event
- [ ] You drafted a minimal-permission YAML snippet to use as a team standard for new workflows

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

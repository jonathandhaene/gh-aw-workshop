<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Enterprise teams need more than individual guardrails — they need policies that enforce consistency, accountability, and cost control at scale._

## 🎯 What You'll Do

Set up organisation-level governance for agentic workflows: enforce a required workflow policy, apply org-wide AI credit caps, and control which models and runners your teams may use. By the end, you will have a governance baseline that works whether your environment is GitHub.com, GitHub Enterprise Cloud (GHEC), or GitHub Enterprise Server (GHES).

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and understand per-workflow credit limits.
- You have **Owner** or **Admin** access to a GitHub organisation (GHEC or GHES).
- Your GHES instance is on version 3.12 or later if you are on GHES — see [Enterprise Setup Considerations](side-quest-enterprise-setup.md).

## Steps

### Understand org-level vs. per-workflow controls

Governance in gh-aw works in two layers:

| Layer | Where it lives | Who sets it |
|---|---|---|
| **Org policy** | Organisation settings → Copilot → Policies | GitHub Org Owner / Admin |
| **Workflow frontmatter** | `.github/workflows/<name>.md` | Workflow author |

Org policy always takes precedence. A workflow author cannot override a restriction that an org admin has set.

### Review Copilot model policies for your organisation

1. Go to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Under **Agentic workflows**, review which models are enabled for your organisation.

By default, organisations on GHEC or GHES have access to GitHub-hosted models only. If your workflows need a specific model (for example, a fine-tuned enterprise model), an administrator must enable it here before workflow authors can reference it in frontmatter.

> [!NOTE]
> On GHES, the available model list is set by your GitHub Enterprise administrator and may be a subset of what is available on GitHub.com. Check with your admin before designing workflows around a specific model.

### Create a required workflow that enforces org standards

GitHub supports [required workflows](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/required-workflows) at the organisation level. You can use a required workflow to audit every agentic workflow run for compliance — for example, to verify that spending limits are present and that the `permissions:` block is minimal.

Create a new workflow file in a central repository (for example, `.github-private` or a dedicated `workflows-policy` repository):

```yaml
---
name: Agentic Workflow Compliance Check
on:
  workflow_call: {}
permissions:
  contents: read
jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Verify spending limits present
        run: |
          grep -r "max-ai-credits" .github/workflows/ || \
            (echo "::error::All agentic workflows must declare max-ai-credits" && exit 1)
```

Then, in your organisation settings under **Actions** → **Required workflows**, select this repository and workflow file. From that point on, every repository in your org must pass this check before a workflow run can be marked as successful.

<details>
<summary>🖥️ GitHub UI path for required workflows</summary>

1. Navigate to your organisation on GitHub.
2. Click **Settings** → **Actions** → **Required workflows**.
3. Click **Add workflow**.
4. Select the repository and workflow file that contains your compliance check.
5. Choose the repositories or repository filter it applies to.
6. Click **Save**.

</details>

### Set org-level AI credit limits

Individual `max-ai-credits` and `max-daily-ai-credits` values in workflow frontmatter cap a single workflow's spend. Org administrators can set a hard ceiling across all workflows using the Copilot billing settings:

1. Go to **Organisation Settings** → **Billing and plans** → **Copilot**.
2. Under **AI credits**, set the **Monthly budget** for Copilot in Actions.
3. Optionally configure an **Alert threshold** (for example, 80%) to receive an email before the budget is exhausted.

> [!IMPORTANT]
> The org-level monthly budget is shared across all agentic workflow runs in the organisation. If your org runs many workflows, set the per-workflow `max-ai-credits` values first, then size the org budget to the expected total.

### Restrict which runners agentic workflows may use

On GHES or GHEC, you can limit runner access by runner group:

1. Go to **Organisation Settings** → **Actions** → **Runner groups**.
2. Edit the runner group that contains your self-hosted runners.
3. Under **Repository access**, select **Selected repositories** and choose which repositories are allowed to use that runner group.

This ensures that sensitive runners (for example, runners with access to an internal network) are only accessible to approved repositories.

### Document your governance baseline

Create a file in your central policy repository (for example `AGENTIC_WORKFLOW_POLICY.md`) that records:

- Allowed models and their use cases
- Per-workflow `max-ai-credits` guidance (for example, "daily-status workflows: 1000 AIC")
- Required frontmatter fields for all agentic workflows
- Contact for exceptions and policy questions

This document becomes the single source of truth for teams onboarding to agentic workflows.

## ✅ Checkpoint

- [ ] You reviewed the Copilot model policies for your organisation and know which models are enabled
- [ ] You created (or planned) a required workflow that enforces a spending-limit check
- [ ] You located the org-level monthly Copilot AI credit budget setting and set or confirmed a value
- [ ] You reviewed your organisation's runner groups and confirmed that sensitive runner groups have restricted repository access
- [ ] You created or updated a governance policy document in a central repository
- [ ] You can explain the difference between org-level policy controls and per-workflow frontmatter limits

<!-- journey: all -->
Want to explore more advanced topics? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at the Organisation Level

> _Enterprise teams need more than working workflows — they need guardrails that apply consistently across every repository in the organisation._

## 🎯 What You'll Do

Learn how GitHub Enterprise administrators can enforce organisation-wide policies on agentic workflows: required approval gates, allowed model lists, maximum credit limits, and required workflow templates. By the end of this step, you'll understand the levers available to admins and know how to configure your own repository to comply with an enterprise policy.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You know whether your environment is `github.com`, GitHub Enterprise Cloud (GHEC), or GitHub Enterprise Server (GHES). If you're unsure, revisit the [Enterprise Setup Side Quest](side-quest-enterprise-setup.md).
- Organisation owner or admin permissions are needed for the admin steps. Learners without those permissions can follow along in read-only mode and apply what they learn when they have access.

## Steps

### Understand the governance model

GitHub Enterprise provides three layers of control for agentic workflows.

| Layer | What it controls | Where it's configured |
|---|---|---|
| **Organisation policy** | Which repositories may run agentic workflows | Organisation → Settings → Copilot |
| **Required workflows** | Workflow templates that automatically run on every repository | Organisation → Settings → Actions → Required workflows |
| **Workflow frontmatter limits** | Per-run and daily AI credit caps | Each workflow's `.md` frontmatter |

You already covered frontmatter limits in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md). This step focuses on the first two layers.

### Enable or restrict agentic workflows at the organisation level

Organisation owners can allow or block Copilot-powered agentic workflow runs across all repositories in one place.

1. In your organisation, click **Settings** → **Copilot** → **Policies**.
2. Under **Agentic workflows**, choose one of:
   - **Allow** — any repository in the organisation can run agentic workflows.
   - **Allow for selected repositories** — only the repositories you pick can run them.
   - **Disable** — no repository in the organisation can trigger an agentic workflow run.
3. Click **Save**.

> [!NOTE]
> On GitHub Enterprise Server, this setting may appear under **Enterprise** → **Policies** → **Copilot** instead of the organisation level. Check with your admin.

### Configure allowed models (GHEC/GHES)

On GitHub Enterprise Cloud and GitHub Enterprise Server 3.14+, admins can restrict which AI models agentic workflows are allowed to call.

1. Navigate to **Organisation Settings** → **Copilot** → **Models**.
2. Enable the models your team has approved (for example, `gpt-4o` or `claude-3-7-sonnet`).
3. Disable any models you haven't reviewed or licensed.

If a workflow frontmatter specifies a model that isn't on the allowed list, the run fails at the agent step with a policy error. This is intentional — it prevents shadow usage of unlicensed or unapproved models.

> [!TIP]
> Document the approved model list in your organisation's internal developer documentation so teams don't spend time debugging policy-blocked runs.

### Create a required workflow template

A required workflow is a reusable Actions workflow that GitHub automatically attaches to every repository in your organisation. You can use this to enforce a standard compliance or reporting step alongside every agentic workflow run.

1. In your organisation, create a repository named `.github` if one doesn't already exist.
2. Inside that repository, create a file at `workflow-templates/agentic-compliance.yml`.
3. Add a workflow that runs a lightweight compliance check — for example, verifying that every agentic workflow file includes a `max-ai-credits` frontmatter key.

A minimal example:

```yaml
name: Agentic Workflow Compliance Check
on:
  push:
    paths:
      - '.github/workflows/*.md'
      - '.github/workflows/*.lock.yml'

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check max-ai-credits is set
        run: |
          for f in .github/workflows/*.md; do
            if ! grep -q 'max-ai-credits' "$f"; then
              echo "Missing max-ai-credits in $f"
              exit 1
            fi
          done
          echo "All workflow files include a credit cap."
```

<!-- markdownlint-disable-next-line MD029 -->
4. In **Organisation Settings** → **Actions** → **Required workflows**, click **Add required workflow** and select your new template.

From that point on, every repository in the organisation must pass this check before a pull request to the default branch can be merged.

<details>
<summary>🖥️ What if I don't have org admin access?</summary>

Share the steps above with your GitHub administrator and ask them to:
- Confirm the current policy settings.
- Add the compliance workflow template to the org `.github` repository.
- Notify repository owners of the new required workflow.

You can prepare the `agentic-compliance.yml` content and supply it to your admin for review and deployment.

</details>

### Audit policy compliance across repositories

Once organisation-level policies are in place, use the audit log to verify compliance.

1. Navigate to **Organisation** → **Settings** → **Audit log**.
2. Search for events with the action `copilot.agentic_workflow_run` to see which repositories triggered agentic runs and whether any runs were blocked by policy.
3. Export the log as a CSV and share it with your security team as evidence of control coverage.

> [!NOTE]
> Audit log events for agentic workflows include the repository name, the workflow file, the model used, and the AIC consumed. See [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) for a full breakdown of available log fields.

## ✅ Checkpoint

- [ ] You can describe the three governance layers (org policy, required workflows, frontmatter limits) and what each controls
- [ ] You located the Copilot policy setting in your organisation (or confirmed with your admin that it exists)
- [ ] You understand how the allowed-model list prevents shadow use of unapproved AI models
- [ ] You have reviewed or drafted a required workflow template that enforces a `max-ai-credits` check
- [ ] You can find agentic workflow run events in the organisation audit log

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

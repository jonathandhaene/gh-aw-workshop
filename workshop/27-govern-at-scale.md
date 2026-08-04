<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows are powerful — and that power needs guardrails when you're running them across teams, repositories, and compliance boundaries._

## 🎯 What You'll Do

You will apply organisation-level governance controls to your agentic workflows: required workflow policies, permission boundaries, and a concurrency strategy to keep AI credit usage predictable at scale. By the end, you will be able to describe how your organisation can allow teams to ship their own agentic workflows without compromising security or budget.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and understand AIC limits.
- You completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and can read audit reports.
- You have a GitHub organisation account (or admin access to one) — or you can follow along in read-only mode.

## Steps

### Understand the governance layers

Agentic workflows inherit the same governance model as standard GitHub Actions, with one additional layer: the AI model call. Four layers protect your organisation:

| Layer | What it controls |
|---|---|
| **Actions policies** | Which workflows are allowed to run and from which sources |
| **Workflow permissions** | What the `GITHUB_TOKEN` can read or write in each repository |
| **AIC budget limits** | How many AI credits a workflow can spend per run and per day |
| **Required workflows** | Mandatory workflows the org forces to run on every repository |

You already covered budget limits (node 26) and workflow permissions (node 16). This step focuses on Actions policies and required workflows.

### Review your organisation's Actions policies

1. Go to your organisation's **Settings** page on GitHub.
2. Under **Actions** → **General**, find the **Actions permissions** section.
3. Confirm that "Allow all actions" is NOT selected for production organisations — scope it to trusted sources or your own repositories.

> [!IMPORTANT]
> <details>
> <summary><b>Enterprise users: complete this check before continuing.</b></summary>
>
> - [ ] Copilot Enterprise is enabled for the organisation
> - [ ] Actions policies are scoped to trusted repositories or internal marketplace only
> - [ ] The Copilot cloud agent feature (`copilot_cloud_agent`) is confirmed as enabled in your GHES admin console (Settings → Copilot)
>
> </details>

### Pin `gh-aw` to a specific version

Unpinned actions drift over time. Pin the `gh aw` CLI version in every workflow frontmatter:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * 1-5"
tools:
  - github
permissions:
  issues: write
  contents: read
gh-aw-version: "1.2.3"   # pin to a known-good version
max-ai-credits: 500
max-daily-ai-credits: 1500
---
```

Pinning prevents a CLI update from silently changing compiled output. Update `gh-aw-version` deliberately after reviewing the [gh-aw release notes](https://github.github.com/gh-aw/reference/releases/).

### Create a required workflow for AIC policy

A _required workflow_ is a workflow in one repository that GitHub enforces on all repositories in the organisation. You can use one to gate every agentic workflow run on a policy check.

1. Create a new repository in your organisation named `agentic-policy` (or equivalent).
1. In that repository, create `.github/workflows/aw-policy-check.yml`:

```yaml
name: Agentic Workflow Policy Check
on:
  workflow_call: {}

jobs:
  policy-check:
    runs-on: ubuntu-latest
    steps:
      - name: Confirm AIC budget field is set
        run: |
          if ! grep -q "max-ai-credits" .github/workflows/*.md; then
            echo "::error::At least one agentic workflow is missing max-ai-credits"
            exit 1
          fi
```

1. In your organisation's **Settings** → **Actions** → **Required workflows**, add `agentic-policy/.github/workflows/aw-policy-check.yml`.

Every repository that runs an agentic workflow will now be checked for a budget field before the workflow executes.

### Set a concurrency limit at the organisation level

Concurrency limits prevent AI credits from running in unbounded parallel bursts across teams. Add a `concurrency:` key to shared workflows so they queue instead of stacking:

```yaml
---
name: daily-status
concurrency:
  group: "agentic-daily-${{ github.repository }}"
  cancel-in-progress: false
---
```

The `cancel-in-progress: false` setting ensures a queued run completes rather than being dropped — useful for compliance-sensitive reporting workflows.

<details>
<summary>🖥️ GitHub UI alternative — edit your workflow file in the browser</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `concurrency:` block to the frontmatter.
4. Click **Commit changes**.
5. Push the recompiled `.lock.yml` from your terminal or Codespace after committing.

</details>

Compile after making frontmatter changes:

```bash
gh aw compile
```

## ✅ Checkpoint

- [ ] You located your organisation's Actions permissions policy and confirmed it is not set to "Allow all actions"
- [ ] Your workflow frontmatter includes `gh-aw-version` pinned to a specific release
- [ ] You understand what a required workflow is and can describe how the policy-check example enforces AIC budget fields
- [ ] Your workflow frontmatter includes a `concurrency:` group that prevents unbounded parallel runs
- [ ] `gh aw compile` passes without errors and the updated `.lock.yml` is committed and pushed
- [ ] You can name at least two governance gaps that organisations hit when scaling agentic workflows without policy controls

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

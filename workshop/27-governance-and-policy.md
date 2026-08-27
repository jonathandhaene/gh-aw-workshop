<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _AI automation is only as trustworthy as the guardrails around it — this step shows you how to apply organisation-level controls so every workflow in your team stays safe, predictable, and compliant._

## 🎯 What You'll Do

You'll review the three layers of governance available to agentic workflows: rate-limiting controls that cap AI spend organisation-wide, concurrency settings that prevent runaway parallelism, and workflow-level approval requirements that ensure a human signs off before sensitive automations write to production. By the end, you'll have updated your daily-status workflow with concurrency controls and know where to point your GitHub administrator for org-level policy settings.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one workflow committed and running in GitHub Actions.
- _(Enterprise users)_ Your GitHub administrator has Copilot Enterprise enabled and can access organisation settings.

## Steps

### Understand the three governance layers

Governance for agentic workflows operates at three levels. Each level is controlled by a different person or role.

| Layer | Controlled by | What it does |
|---|---|---|
| **Organisation rate limits** | GitHub admin | Caps total AI Credits consumed per day across all workflows in the org |
| **Workflow concurrency** | Workflow author (you) | Prevents multiple copies of the same workflow from running simultaneously |
| **Approval requirements** | Repository admin | Requires a human approver before a workflow can write to protected targets |

You control the middle layer. Your admin controls the outer layers. Together they form a defence-in-depth approach to AI automation.

### Add a concurrency group to your workflow

Without concurrency controls, a busy repository can trigger the same workflow twice in quick succession. If both runs read the same data and post the same output, you pay for both runs and your readers see duplicate content.

Open your daily-status workflow file (e.g. `daily-report-status.md`) and add a `concurrency` block to the frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
  workflow_dispatch:
concurrency:
  group: daily-status
  cancel-in-progress: false
timeout-minutes: 10
max-ai-credits: 1000
---
```

`group: daily-status` gives this workflow a unique concurrency key. A second run will queue behind the first rather than running in parallel. Setting `cancel-in-progress: false` means the queued run still completes — it does not get cancelled. Use `cancel-in-progress: true` only for workflows where a stale run has no value (for example, a PR diff summary when a newer commit has already arrived).

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow `.md` file in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `concurrency:` block shown above between `workflow_dispatch:` and `timeout-minutes:`.
4. Click **Commit changes**, write a short message such as `Add concurrency group to daily-status workflow`, and commit directly to your branch.

You do not need to compile locally — the next workflow run will use the updated file.

</details>

After editing, recompile locally if you have a terminal:

```bash
gh aw compile
```

Commit both the `.md` and the regenerated `.lock.yml`.

### Review organisation-level rate-limiting controls

[Rate-limiting controls](https://github.github.com/gh-aw/reference/rate-limiting-controls/) sit at the organisation level and are set by a GitHub administrator — not in your workflow file. They define a ceiling on how many AI Credits the entire organisation can consume in a rolling window.

As a workflow author, you do not configure these controls directly. What you _can_ do is:

- **Ask your admin** what the org-level limit is, so you can calibrate your own `max-daily-ai-credits` values accordingly.
- **Set `max-ai-credits` and `max-daily-ai-credits` in your workflow** (covered in [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)) so your workflow stays within its fair share of the org budget.
- **Monitor usage** in the GitHub billing dashboard and flag unusual spikes to your admin before they hit the org ceiling.

> [!NOTE]
> On GitHub Enterprise Server (GHES), org-level rate limits are configured by your site administrator in the **Copilot** section of the management console. Confirm with your admin whether limits apply per-org or enterprise-wide on your deployment.

### Apply approval requirements for sensitive workflows

Some workflows write to production-critical targets — for example, creating releases, pushing to protected branches, or modifying repository settings. GitHub lets repository admins require a human approval before a workflow can access protected environments.

To protect a workflow that writes to a sensitive target:

1. In your repository, go to **Settings** → **Environments** → **New environment**.
2. Name the environment (for example, `production`).
3. Under **Deployment protection rules**, enable **Required reviewers** and add one or more approvers.
4. In your workflow frontmatter, declare the environment:

```yaml
---
name: Release Publisher
on:
  workflow_dispatch:
environment: production
---
```

When this workflow is triggered, GitHub pauses it and notifies the required reviewer. The workflow resumes only after a human approves the run.

> [!TIP]
> Required reviewers are most valuable for workflows with `contents: write` or `packages: write` permission — those that can change code or publish artefacts. Read-only reporting workflows rarely need this protection.

### Check concurrency settings for parallel workflows

If you run multiple agentic workflows in the same repository, give each one its own concurrency group name. Sharing a group name across unrelated workflows will cause them to queue behind each other unnecessarily.

```yaml
# In daily-report-status.md
concurrency:
  group: daily-status
  cancel-in-progress: false

# In pr-reviewer.md (separate file)
concurrency:
  group: pr-review-${{ github.event.pull_request.number }}
  cancel-in-progress: true
```

The PR reviewer uses a dynamic group key so each pull request gets its own concurrency slot. A new push to the same PR cancels the in-progress review and starts fresh — which is exactly the right behaviour for diff-based workflows.

Learn more about concurrency options in the [concurrency reference](https://github.github.com/gh-aw/reference/concurrency/).

## ✅ Checkpoint

- [ ] You added a `concurrency:` block with a named group to your daily-status workflow
- [ ] You recompiled (`gh aw compile`) and committed both the `.md` and `.lock.yml` files, or committed the `.md` change via the GitHub UI
- [ ] You can explain the difference between org-level rate limits (admin-controlled) and workflow-level `max-ai-credits` (author-controlled)
- [ ] You know how to set up a required-reviewer environment in GitHub Settings for sensitive workflows
- [ ] _(Enterprise users)_ You confirmed with your GitHub administrator what org-level AI Credit rate limits apply to your deployment

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern and Audit Agentic Workflows at Scale

> _When one person runs a workflow it's a tool; when a whole organisation runs them it's infrastructure — and infrastructure needs governance._

## 🎯 What You'll Do

You'll apply organisation-level controls to your agentic workflows: a required workflow policy, an approved-template catalog, and a compliance checklist your team can use before promoting any workflow to production.

By the end of this step, you'll have a repeatable pattern for keeping agentic workflows safe, auditable, and aligned with your organisation's security posture.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or any step in the advanced track.
- You have admin or org-owner access to a GitHub organisation (or you can follow along and apply concepts when you do).

## Why Governance Matters

Agentic workflows act without a human clicking **Approve**. Three risks grow as your organisation scales:

- **Drift**: teams add permissions or tools that weren't reviewed.
- **Overspend**: AIC costs accumulate across dozens of uncoordinated workflows.
- **Audit gaps**: security teams can't reconstruct what an agent did or why.

## Define and Enforce a Required Workflow

GitHub organisations can require that every repository runs a specific workflow — for example, a compliance scan — before any other workflow result counts. For agentic workflows, use this to enforce a shared approval step.

In your organisation settings, navigate to **Actions → Required workflows** and add a workflow stored in a `.github` repository.

A minimal required compliance workflow checks that every agentic workflow file:

- declares explicit `permissions:` (no `write-all`)
- includes at least one `safe-outputs:` limit
- sets a `timeout-minutes` value

> [!NOTE]
> Required workflows run in the context of the repository that triggered them, but the workflow definition lives in your `.github` org-level repository. This lets org admins update the policy without touching every team repository.

## Build an Approved-Template Catalog

The `gh aw add` command installs a workflow from a catalog URL. Publish vetted workflow templates to a central repository, then share the install command:

```bash
gh aw add https://github.com/<your-org>/.github-workflows/blob/main/catalog/pr-reviewer.md
```

Store catalog entries alongside a `CODEOWNERS` file so changes require approval from the security team.

<details>
<summary>🖥️ GitHub UI alternative — add a catalog entry</summary>

1. In your `.github-workflows` repository, click **Add file → Create new file**.
2. Name the file `catalog/<workflow-name>.md` and paste the reviewed workflow source.
3. Click **Commit new file** → choose **Create a new branch** → open a pull request for review.
4. After approval, merge — the new catalog entry is immediately available via `gh aw add`.

</details>

## Complete the Pre-Production Checklist

Before promoting any workflow to production, verify each item. Share this as a pull-request template in your team's workflow repository.

| Control | How to verify |
|---------|--------------|
| Minimal `permissions:` | No `write-all`; each scope has a documented reason |
| `safe-outputs:` limits set | Each write output has an explicit `limit:` value |
| `timeout-minutes` defined | Appropriate for the expected run duration |
| `max-ai-credits` set | Matches your budget model from Step 26 |
| `network.allowed-domains` reviewed | Only domains the workflow legitimately needs |
| Lock file committed | `.lock.yml` is current and committed alongside `.md` |
| Required workflow passes | Compliance scan green in Actions |

> [!TIP]
> Store this checklist as `.github/PULL_REQUEST_TEMPLATE/agentic-workflow-review.md` in your workflow repository. Pull requests that add or change a workflow file will automatically show the checklist.

## A Compliant Workflow Header

Here is a reference frontmatter that satisfies every checklist control:

```yaml
---
name: pr-compliance-check
on:
  pull_request:
    types: [opened, synchronize]
permissions:
  pull-requests: write
  contents: read
safe-outputs:
  create-issue-comment:
    limit: 1
timeout-minutes: 10
max-ai-credits: 500
network:
  allowed-domains:
    - api.github.com
---
```

Use it as a starting point for new catalog entries.

## ✅ Checkpoint

- [ ] You can explain the difference between per-workflow controls and organisation-level required workflows
- [ ] You know how to set up a required workflow in GitHub organisation settings
- [ ] You have a central location (or a plan for one) to host your approved-workflow catalog
- [ ] You identified at least one workflow in your repository that would fail the pre-production checklist today
- [ ] You added or planned a PR template checklist for agentic workflow reviews
- [ ] You can describe the three governance risks (drift, overspend, audit gaps) and the control that addresses each

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

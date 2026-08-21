<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Roll Out Agentic Workflows Across Your Organisation

> _A workflow that works for one team can work for hundreds — but only if it lands safely in every environment._

## 🎯 What You'll Do

You'll plan a staged rollout of an agentic workflow to multiple teams or repositories in your organisation. By the end, you'll have a governance checklist, a reusable template strategy, and confidence that your workflow behaves consistently whether it runs on `github.com`, GitHub Enterprise Cloud (GHEC), or GitHub Enterprise Server (GHES) 3.12+.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow compiles cleanly with `gh aw compile` (no errors).
- You have at least one repository where the workflow is already running successfully.
- If you are on GHES, confirm Copilot Enterprise is enabled organisation-wide (see [Side Quest: Enterprise Setup](side-quest-enterprise-setup.md)).

## Steps

### Audit permissions and secrets before scaling

Agentic workflows run as the Copilot cloud agent, but they still rely on the repository-level `GITHUB_TOKEN` and any secrets you configure. Before rolling out to new repositories, check that each destination:

1. Has **Copilot Enterprise** enabled at the organisation level (GHEC/GHES) or a Copilot Business/Enterprise seat assigned to the organisation (github.com).
2. Has Actions enabled — visit **Settings → Actions → General** and confirm "Allow all actions and reusable workflows" or an appropriate policy is set.
3. Does **not** have a branch protection rule that would block the workflow's commits or pull requests.

> [!TIP]
> For GHES deployments, also confirm that your instance can reach `api.github.com` (or your GHES API endpoint) from the runner host. Network-isolated instances require proxy configuration — see [Side Quest: Enterprise Setup](side-quest-enterprise-setup.md).

### Create a shared workflow template

Instead of copying workflow files into every repository by hand, publish the workflow as a [reusable workflow](18-share-and-reuse.md) in a central repository (often `.github` in your organisation).

1. In your organisation, create or open the `.github` repository.
2. Place your compiled workflow file in `.github/workflows/` of that repository.
3. In each consuming repository, reference it with a `uses:` key in a standard Actions workflow:

```yaml
jobs:
  agentic-report:
    uses: my-org/.github/.github/workflows/daily-report.lock.yml@main
```

> [!NOTE]
> Only the compiled `.lock.yml` file is consumed by standard Actions `uses:` references. The source `.md` file stays in the authoring repository.

### Apply organisation-level permissions

Agentic workflows obey repository-level `permissions:` blocks in the frontmatter. When rolling out across an organisation, use the most restrictive set of permissions that still lets the workflow do its job:

```yaml
---
permissions:
  contents: read
  issues: write
  pull-requests: read
---
```

Avoid `contents: write` unless the workflow must commit files. Prefer read-only data access and safe-output patterns for writes.

### Stage the rollout with a pilot group

Roll out in waves to reduce risk:

| Wave | Scope | Goal |
|------|-------|------|
| 1 — Pilot | 2–3 volunteer teams | Validate behaviour, cost, and permissions |
| 2 — Expansion | 20–30 repositories | Catch edge cases (branch protections, GHES proxies) |
| 3 — Org-wide | All repositories | Full rollout with monitoring in place |

After each wave, review the Actions run history and check your AI Credit consumption in the billing dashboard before proceeding.

### Set cost guardrails at the organisation level

Revisit the budget controls from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) and apply them in the shared template so every consuming repository inherits them automatically:

```yaml
---
max-ai-credits: 800
max-daily-ai-credits: 2000
timeout-minutes: 10
---
```

This ensures that a runaway workflow in one repository cannot exhaust the organisation's AI Credit allocation.

### Document rollout decisions for your team

Create a short rollout runbook in your organisation's `.github` repository (for example, `docs/agentic-workflows-rollout.md`). Include:

- Which workflows are approved for organisation-wide use.
- The permission model each workflow uses.
- How to request a new agentic workflow be added to the shared template.
- Who to contact if a workflow behaves unexpectedly.

Good documentation turns a one-person experiment into a team capability.

## ✅ Checkpoint

- [ ] You verified that Copilot Enterprise (or equivalent seat) is enabled for your organisation
- [ ] You confirmed Actions is enabled on at least two target repositories
- [ ] You published the compiled workflow to the `.github` organisation repository
- [ ] You applied a minimal-permission `permissions:` block to the shared workflow
- [ ] You defined a pilot group and outlined a staged rollout plan
- [ ] You added `max-ai-credits` and `max-daily-ai-credits` to the shared template
- [ ] You created or outlined a rollout runbook for your team

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

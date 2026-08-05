<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Shipping one workflow is great — governing a library of them across your organisation is how you keep teams moving safely._

## 🎯 What You'll Do

Add an approval gate and a reuse policy to your agentic workflow. You will configure a required reviewer in GitHub Actions environments, centralise your workflow in a shared repository, and verify that the governance guardrails hold across a simulated team contribution.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or at least [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have admin or owner permissions on your practice repository (needed to create a deployment environment).
- If you are on GitHub Enterprise Cloud or GitHub Enterprise Server, confirm that deployment protection rules are enabled for your organisation.

## Steps

### Understand the governance challenge

When one team runs one workflow, oversight is easy. When many teams run many workflows, three problems emerge:

1. **Unreviewed changes** — a prompt edit can quietly change what an agent writes, reads, or posts.
2. **Credential sprawl** — every team managing their own secrets and tokens creates audit gaps.
3. **Duplication** — teams rebuild the same patterns without learning from each other.

Approval gates, a centralised catalog, and clear reuse policies address all three.

### Create a protected deployment environment

A GitHub Actions **environment** lets you require a human reviewer before a job runs. You can gate any agentic workflow step behind this approval without changing its logic.

1. In your practice repository on GitHub, click **Settings** → **Environments** → **New environment**.
2. Name it `production-agentic`.
3. Under **Deployment protection rules**, enable **Required reviewers** and add yourself (or a teammate) as a reviewer.
4. Click **Save protection rules**.

![Environment protection rules settings screen](images/27-environment-protection-rules.png)

> [!TIP]
> On GitHub Enterprise Cloud, your organisation admin may already have org-level required reviewers configured. If the **Required reviewers** option is greyed out, check with your admin before proceeding.

### Wire the environment into your workflow

Open your `daily-status.md` workflow file and add `environment: production-agentic` to the job that posts the summary. The frontmatter change is small:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
  workflow_dispatch:

jobs:
  report:
    runs-on: ubuntu-latest
    environment: production-agentic
    permissions:
      issues: write
      contents: read
---
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to edit the file.
3. Add `environment: production-agentic` inside the `jobs.report:` block as shown above.
4. Click **Commit changes** and choose **Commit directly to the main branch**.

</details>

After saving the file, recompile locally if you have `gh aw compile` available, then push. If you are working entirely in the UI, GitHub Actions will recompile on the next run.

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "chore: add production-agentic environment gate"
git push
```

### Trigger the workflow and approve the run

1. In your repository, go to **Actions** → select the **Daily Status Report** workflow → click **Run workflow**.
2. The run will pause at the `report` job with a yellow banner: **"Waiting for review"**.
3. Click **Review deployments**, select `production-agentic`, add a note if you like, and click **Approve and deploy**.
4. Watch the run complete. Note that the approval event appears in the run log alongside the reviewer's name.

This approval event is also captured in GitHub's audit log — something enterprise compliance teams require.

### Document a team reuse policy

Governance is not only technical. Teams need to know _how_ to contribute new workflows and _who_ approves them.

Create a short policy file in your repository at `.github/AGENTIC_WORKFLOWS.md` with at minimum:

- The shared repository or catalog where approved workflows live.
- The review process for new workflow submissions (reference the `production-agentic` environment).
- A link to cost and budget guidelines from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).

```bash
cat > .github/AGENTIC_WORKFLOWS.md << 'EOF'
# Agentic Workflow Governance

All agentic workflows in this repository require approval before deploying to production.

## Approval process

1. Open a pull request with your workflow `.md` and compiled `.lock.yml`.
2. A workflow maintainer reviews and approves the PR.
3. Deployments to the `production-agentic` environment require a second reviewer approval at run time.

## Cost controls

Every workflow must include `max-ai-credits` and `timeout-minutes` in its frontmatter.
See [Manage Costs and AI Credit Budgets](../workshop/26-manage-costs-and-budgets.md).

## Catalog

Approved shared workflows are published to the team catalog. Install with `gh aw add`.
EOF
git add .github/AGENTIC_WORKFLOWS.md
git commit -m "docs: add agentic workflow governance policy"
git push
```

> [!NOTE]
> For GitHub Enterprise Cloud, your organisation may already have a `.github` repository that hosts org-wide policy files. If so, add `AGENTIC_WORKFLOWS.md` there instead so it applies to all repositories in the org.

## ✅ Checkpoint

- [ ] You created the `production-agentic` environment with at least one required reviewer
- [ ] Your `daily-status.md` frontmatter includes `environment: production-agentic` in the job block
- [ ] `gh aw compile` completed without errors (or GitHub Actions recompiled on push)
- [ ] You triggered a manual run, saw the "Waiting for review" pause, and approved it
- [ ] The approval event appears in the Actions run log with your name
- [ ] You created `.github/AGENTIC_WORKFLOWS.md` with a reuse policy and cost controls reference

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

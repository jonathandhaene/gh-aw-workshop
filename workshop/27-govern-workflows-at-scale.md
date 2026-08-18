<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _A workflow that works for one repository needs organisation-wide guardrails before you roll it out to dozens of teams._

## 🎯 What You'll Do

You'll set up organisation-level policies that control which agentic workflows teams can run, require pull-request review before compiled lock files are merged, and document a lightweight rollout checklist so new teams can adopt workflows safely.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are an **organisation owner** (or can pair with one for the policy steps).
- _(Enterprise users)_ Your GitHub administrator has confirmed that enterprise policy settings are accessible via the organisation settings panel.

## Steps

### Understand what governance covers

Governance for agentic workflows has three layers:

| Layer | What it controls |
|---|---|
| **Organisation policy** | Which repositories can run Copilot-powered workflow jobs |
| **Branch protection** | Who can merge compiled lock files into the default branch |
| **Workflow-level guardrails** | `permissions:`, `safe-outputs`, `network.allowed-domains`, and credit limits |

You configured the third layer in earlier steps. This step focuses on the first two.

### Enable Copilot for workflows at the organisation level

1. In your organisation, click **Settings** → **Copilot** → **Policies**.
2. Set **Allow Copilot in GitHub Actions** to **Allowed for selected repositories**.
3. Click **Save**.

> [!NOTE]
> On GHEC, this may be inherited from an enterprise-level policy. If the option is greyed out, ask your GitHub Enterprise administrator.

### Protect the lock file with a branch protection rule

Compiled lock files (`*.lock.yml`) are the final artefact that GitHub Actions executes. Requiring a review before they are merged prevents a solo author from shipping unreviewed AI credit allocations or permission changes.

1. In your repository, click **Settings** → **Branches**.
2. Next to your default branch (usually `main`), click **Edit** (or **Add rule** if none exists).
3. Enable **Require a pull request before merging**.
4. Enable **Require approvals** and set the required number to **1** (or higher for sensitive repositories).
5. Under **Require status checks to pass before merging**, add any CI checks that run on your workflow files.
6. Click **Save changes**.

<details>
<summary>🖥️ GitHub UI alternative — add a CODEOWNERS rule for lock files</summary>

Pair branch protection with a `CODEOWNERS` file so the right team is always auto-requested as a reviewer when a lock file changes.

1. In your repository, create or open `.github/CODEOWNERS`.
2. Add a line:

   ```
   *.lock.yml @your-org/workflow-reviewers
   ```

3. Commit and push. GitHub automatically requests a review from the `workflow-reviewers` team whenever a lock file changes in a pull request.

</details>

### Document a team rollout checklist

A repeatable checklist reduces onboarding time and catches missing steps before a workflow reaches production.

Create a short entry in your team wiki or `WORKFLOW_GOVERNANCE.md`. At minimum, check:

- [ ] Workflow `.md` reviewed for minimal `permissions:` scope
- [ ] `max-ai-credits` and `max-daily-ai-credits` set in frontmatter
- [ ] `network.allowed-domains` restricted to required domains only
- [ ] Lock file reviewed and approved by a second team member
- [ ] First run triggered manually and output verified
- [ ] Alert or issue configured for failed runs

> [!TIP]
> Paste this list into `.github/pull_request_template.md` so every lock-file PR is checked automatically.

### Review organisation-wide usage periodically

Once multiple repositories are running agentic workflows, run a monthly review to catch runaway credit use or over-scoped permissions.

1. Go to **Settings** → **Billing and plans** → **Usage** at the organisation level.
2. Filter by **Agentic Workflows** to see per-repository AIC consumption.
3. Flag any repository that exceeds an agreed monthly threshold.

> [!TIP]
> On GitHub Enterprise Server, the equivalent view is under the **Site Admin** panel → **Usage reports**.

## ✅ Checkpoint

- [ ] You set the organisation Copilot-in-Actions policy to **Allowed for selected repositories**
- [ ] Your default branch now requires at least one pull-request approval before a lock file can be merged
- [ ] You created or updated a `CODEOWNERS` rule so lock-file changes request the right reviewers automatically
- [ ] You have a written rollout checklist your team will follow before any new workflow reaches production
- [ ] You reviewed the organisation billing dashboard and can identify the top AIC consumer this cycle

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

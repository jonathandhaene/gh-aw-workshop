<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows

> _Automation that can write to your repository needs guardrails — governance policies make your agentic workflows safe to run at team and enterprise scale._

## 🎯 What You'll Do

You'll apply three practical governance controls to your daily-status workflow: a required reviewer on agent-created pull requests, a branch protection rule that gates the agentic output branch, and an organisation-level policy entry for your workflow file. By the end, you'll have a reproducible governance checklist that your team can adopt.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your daily-status workflow uses `safe-outputs: create-pull-request` so it writes through PRs rather than directly to `main`.
- _(Enterprise users)_ Your GitHub administrator has repository or organisation admin rights to set branch protection rules and organisation policies.

## Steps

### Understand the governance model for agentic workflows

An agentic workflow can read issues, create branches, open pull requests, and post comments — all without a human clicking anything. The same productivity that makes it useful also means an unreviewed agent output could land in `main`.

GitHub's existing governance primitives apply here:

| Control | What it prevents |
|---------|-----------------|
| Branch protection rules | Agent PRs merging without a human approver |
| Required status checks | Merging before CI passes on agent-created branches |
| CODEOWNERS | Specific people get auto-requested for review on relevant file paths |
| Organisation policy | Admins can limit which repositories are allowed to use agentic workflows |

None of these require new tooling — you are wiring up controls you may already know.

### Protect the branch your agent targets

Your daily-status workflow creates a pull request against `main` (or the default branch). Add a protection rule so that branch cannot be pushed to directly and any PR requires at least one approving review.

**GitHub UI path (recommended):**

1. Open your practice repository on GitHub.
2. Click **Settings** → **Branches** → **Add branch protection rule** (or **Add classic branch protection rule** on GHES).
3. Set **Branch name pattern** to `main`.
4. Enable **Require a pull request before merging** and set **Required approvals** to `1`.
5. Enable **Require status checks to pass before merging** — add your Actions workflow as a required check if it runs tests.
6. Click **Create** or **Save changes**.

> [!TIP]
> On GitHub Enterprise Server, the branch protection UI is under **Settings → Branches** and looks almost identical. Ask your admin if the **Required approvals** option is greyed out — it may require a policy setting at the organisation level.

<details>
<summary>🖥️ CLI alternative (gh)</summary>

```bash
gh api \
  --method PUT \
  -H "Accept: application/vnd.github+json" \
  /repos/{owner}/{repo}/branches/main/protection \
  --input - <<'EOF'
{
  "required_status_checks": null,
  "enforce_admins": false,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1
  },
  "restrictions": null
}
EOF
```

Replace `{owner}` and `{repo}` with your values.

</details>

### Add a CODEOWNERS rule for your workflow files

A `CODEOWNERS` file tells GitHub who to auto-request as a reviewer when specific files change. Add a rule for `.github/workflows/` so that any agent-created PR touching workflow files always routes to a human reviewer.

1. Open (or create) `.github/CODEOWNERS` in your repository.
2. Add this line, replacing `@your-team-or-username` with a team handle (e.g., `@acme/platform-eng`) or your username: `.github/workflows/ @your-team-or-username`
3. Commit and push the file (or save it via the GitHub editor).

Now any pull request that modifies a file under `.github/workflows/` — including agent-created workflow updates — will automatically request a review from the owners you listed.

### Document your workflow's governance entry

For teams that maintain a central registry of approved automations, add a short governance entry for your workflow. A simple markdown table in your team wiki or repository README works well:

| Workflow | Trigger | Writes to | Reviewer | Cost cap |
|----------|---------|-----------|----------|----------|
| `daily-status.md` | `schedule: daily on weekdays` | Pull request on `main` | `@your-team` | 500 AIC / run |

This table makes the workflow visible and auditable without requiring a dedicated tool.

> [!NOTE]
> GitHub Enterprise Cloud (GHEC) organisations can enforce agentic workflow policies at the organisation level — for example, requiring that all `.md` workflow files pass a linting check or be approved by a code owner before being compiled and run. Ask your GitHub administrator about organisation-level Actions policies if you need tighter control.

## ✅ Checkpoint

- [ ] Your `main` branch has a protection rule that requires at least one approving review before merging
- [ ] A `CODEOWNERS` entry routes `.github/workflows/` changes to your team or yourself
- [ ] You can explain the difference between branch protection (merge gate) and CODEOWNERS (reviewer routing)
- [ ] You added a governance table entry documenting your workflow's trigger, write targets, reviewer, and cost cap
- [ ] _(Enterprise users)_ You confirmed with your admin whether an org-level Actions policy applies to your repository

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

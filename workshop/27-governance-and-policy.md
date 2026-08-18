<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _As agentic workflows expand across an organisation, teams need clear policies, approval gates, and change controls — so every run is traceable, reviewable, and safe._

## 🎯 What You'll Do

You'll learn the governance levers that enterprise teams use to control who can create workflows, what they can do, and how changes are reviewed. By the end of this step you'll have applied at least one policy control to your own workflow repository and know where to go for organisation-wide rollout.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- _(GHES/GHEC users)_ You can view your organisation's Actions settings page. If you cannot, ask your GitHub admin to walk through the permission sections with you.
- Your workflow source file and lock file are committed and pushed.

## Steps

### Understand the governance layers

Governance for agentic workflows operates at three levels:

| Level | Controlled by | What you can enforce |
|---|---|---|
| **Repository** | Repo settings and CODEOWNERS | Who can approve workflow changes; branch protection on the workflow branch |
| **Organisation** | Org Actions settings | Which actions/extensions are allowed; required approvals for enterprise runners |
| **Enterprise** | Enterprise policy | Copilot model access policies; audit log retention; runner fleet restrictions |

You don't need to configure all three layers — start with the repository layer and escalate as needed.

### Protect your workflow source files with CODEOWNERS

A `CODEOWNERS` file routes pull requests that touch a path to specific reviewers. Adding a rule for your workflows directory means every change to an agentic workflow automatically requests review from the right people.

In the GitHub UI, open your practice repository. Click **Add file → Create new file** and name it `.github/CODEOWNERS`.

Add a rule that requires review for any workflow file change:

```text
.github/workflows/*.md @your-org/workflow-approvers
.github/workflows/*.lock.yml @your-org/workflow-approvers
```

Replace `@your-org/workflow-approvers` with the GitHub team or individual usernames responsible for reviewing workflow changes. Click **Commit new file**.

> [!NOTE]
> CODEOWNERS only works when branch protection is also enabled. The next section adds that guard.

### Enable branch protection with required reviews

Open **Settings → Branches** in your practice repository.

1. Click **Add branch protection rule**.
2. Set the branch name pattern to `main` (or the branch your workflows are committed to).
3. Enable **Require a pull request before merging**.
4. Set **Required approvals** to at least `1`.
5. Enable **Require review from Code Owners**.
6. Click **Create**.

Now any pull request that touches a workflow file will block merge until a designated reviewer approves it. This creates an auditable approval trail for every workflow change.

### Review the organisation-level Actions policy

If you are in an organisation, the admin can restrict which actions and extensions are allowed. To see the current policy:

1. Open your organisation's **Settings → Actions → General**.
2. Under **Actions permissions**, confirm whether the policy allows all actions or restricts to a specific set. If your organisation uses an allowlist, `gh-aw` must be on it.
3. Under **Workflow permissions**, confirm the default token permissions match your security posture (typically `Read repository contents and metadata` for least-privilege workflows).

> [!TIP]
> On GHES, the enterprise policy page in the GitHub admin console applies the same controls across all organisations simultaneously — ask your admin to confirm the instance-level setting before relying on organisation-level overrides.

### Add a required status check (optional but recommended)

If your team uses CI to validate workflow lock files, you can require that check to pass before any workflow PR merges. In the branch protection rule you created earlier, enable **Require status checks to pass before merging** and select the check name from your CI pipeline.

This ensures that `gh aw compile` output is verified before a workflow change reaches production.

### Document your approval workflow

Create a short runbook (a GitHub issue, a wiki page, or a `CONTRIBUTING.md` entry) that answers:

- Who is on the `workflow-approvers` team?
- What does a reviewer check before approving a workflow change?
- How is an emergency change escalated if the usual reviewer is unavailable?

Even one paragraph of documented process dramatically reduces review time and keeps your audit trail consistent.

## ✅ Checkpoint

- [ ] You added a `CODEOWNERS` entry that routes workflow changes to a reviewer
- [ ] You enabled branch protection with required review from Code Owners on your main branch
- [ ] You reviewed the organisation Actions permissions policy (or confirmed it with your admin)
- [ ] You confirmed the default workflow token permission aligns with your security requirements
- [ ] You know which governance layer to contact for each type of workflow change

<!-- journey: all -->
Want to explore more advanced topics? Return to [What's Next? Keep Exploring](14-next-steps.md) or visit the [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/).
<!-- /journey -->

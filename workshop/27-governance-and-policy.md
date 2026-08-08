<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Running one workflow is exciting — rolling out dozens to a team requires guardrails so every workflow stays safe, auditable, and within policy._

## 🎯 What You'll Do

You'll learn how to apply organisation-level controls to agentic workflows: required reviewers, branch-protection policies, reusable workflow permissions, and a lightweight governance checklist your team can adopt before any new workflow goes to production.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have write access to your practice repository's settings (or you can follow along with screenshots).
- _(Enterprise users)_ Ask your GitHub administrator whether Organisation Actions policies are locked before you begin.

## Steps

### Understand the governance problem

As agentic workflows grow across repos and teams, the risks compound:

- A workflow with `contents: write` and no reviewer could silently rewrite protected files.
- A misconfigured `tools:` block could open an MCP connection to an unvetted service.
- Cost spikes from a runaway scheduled workflow can surprise a billing admin.

Governance means deciding, documenting, and enforcing the rules that keep those risks manageable.

### Apply branch-protection rules to your default branch

Your agentic workflows open pull requests and push commits. Branch protection ensures a human reviews what the agent produces before it merges.

In your repository, go to **Settings → Branches** and add a rule for `main` (or your default branch):

1. Enable **Require a pull request before merging**.
2. Set **Required approvals** to at least `1`.
3. Enable **Dismiss stale pull request approvals when new commits are pushed**.
4. Click **Save changes**.

Now the PR your workflow creates cannot self-merge without a human approval.

> [!TIP]
> If your workflow is the only actor pushing to a dedicated `reports/` branch, you can scope the branch-protection rule narrowly — for example `reports/*` — and leave `main` under a stricter policy.

### Use required reviewers on sensitive workflows

For workflows that write to sensitive areas (documentation, configuration, secrets), add a [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) entry so that any PR touching those paths requires approval from a designated reviewer.

Create or edit `.github/CODEOWNERS`:

```
# Require a senior engineer to review any agentic workflow definition
.github/workflows/*.md @your-org/workflow-reviewers
.github/workflows/*.lock.yml @your-org/workflow-reviewers
```

Commit this file to `main`. Now every workflow PR automatically requests a review from the `workflow-reviewers` team.

<details>
<summary>🖥️ GitHub UI alternative — edit CODEOWNERS</summary>

1. Navigate to your repository on GitHub.
2. Click **Add file → Create new file**.
3. Type `.github/CODEOWNERS` as the file name.
4. Paste the CODEOWNERS content above and replace the team slug with your own.
5. Click **Commit new file**.

</details>

### Document your governance checklist

Create a lightweight pre-production checklist that any workflow author runs before enabling a workflow in production. Add it to your repository wiki or a `docs/workflow-governance.md` file:

```markdown
## Agentic Workflow Pre-Production Checklist

- [ ] `permissions:` uses the minimum required scope (no `contents: write` unless the workflow commits)
- [ ] `safe-outputs:` is limited to the write types the workflow actually needs
- [ ] `network.allowed` lists only the domains the workflow must reach
- [ ] `max-ai-credits` and `max-daily-ai-credits` are set
- [ ] Branch protection prevents self-merge on the target branch
- [ ] A CODEOWNERS rule routes workflow PRs to a named reviewer
- [ ] The workflow has been run at least once in a test repository before production
```

> [!NOTE]
> For GitHub Enterprise Server deployments, work with your administrator to embed this checklist in your org's [required status checks](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches#require-status-checks-before-merging) or pull-request templates.

### Limit which workflows can run in your organisation

Organisation owners can restrict which actions and reusable workflows are permitted. In **Organisation Settings → Actions → General**, set the policy to:

- **Allow select actions and reusable workflows** — list your approved workflow repository slugs.
- Block all external MCP server connections by default; approve them case-by-case.

This ensures that new workflow files cannot silently pull in unapproved tooling.

## ✅ Checkpoint

- [ ] Your default branch has a branch-protection rule requiring at least one pull-request approval
- [ ] A CODEOWNERS entry routes pull requests touching `.github/workflows/*.md` to a named team or user
- [ ] You have a pre-production checklist (in a file or wiki page) covering permissions, safe-outputs, cost limits, and reviewer assignment
- [ ] You can explain, in one sentence, why `contents: write` deserves extra scrutiny in an agentic workflow
- [ ] _(Enterprise)_ You have confirmed with your admin whether org-level Actions policies restrict which workflows can run

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

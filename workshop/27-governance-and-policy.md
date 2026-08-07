<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enforce Governance and Policy for Agentic Workflows

> _Giving an AI agent write access to your repository is powerful — and it deserves the same branch protection and review gates you apply to human contributors._

## 🎯 What You'll Do

Set up organisation-level controls that keep agentic workflow pull requests safe. You will add a branch protection rule requiring human review before any AI-generated PR can merge, confirm that your workflow's `permissions:` block follows least-privilege, and optionally add a CODEOWNERS file so the right team is always requested as a reviewer.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or reached this node from [What's Next? Keep Exploring](14-next-steps.md).
- You have at least one agentic workflow that creates pull requests (for example, the PR reviewer from [Build Your First Event-Driven Workflow](14b-pr-reviewer-workflow.md)).
- You have admin access to your practice repository (or a GitHub organisation admin is available to help).

## Steps

### Understand why governance matters for agentic workflows

A well-written agentic workflow follows the principle of least privilege: it requests only the permissions it needs, and it surfaces outputs through pull requests or issue comments rather than committing directly to the default branch.

Branch protection rules enforce that contract from the outside. Even if a workflow's brief is later changed to request broader access, the branch rule acts as a safety net.

> [!TIP]
> For enterprise teams, pairing branch protection with a CODEOWNERS file gives you a clear audit trail: every AI-generated PR is reviewed by a named human before merging.

### Review your workflow's permissions block

Open your workflow's `.md` file. Confirm that the `permissions:` block requests only what the workflow truly needs:

```yaml
permissions:
  contents: read
  pull-requests: write
```

If your workflow only creates pull requests — it does not commit directly — you do not need `contents: write`. Change it to `contents: read`.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/` in your repository on GitHub.
2. Click the workflow file name, then click the **pencil icon (✏️)** to edit.
3. Update the `permissions:` block, then click **Commit changes**.

</details>

After editing the file, recompile:

```bash
gh aw compile
```

### Add a branch protection rule

1. In your repository, go to **Settings** → **Branches**.
2. Under **Branch protection rules**, click **Add rule** (or **Add branch ruleset** if your organisation uses rulesets).
3. Set **Branch name pattern** to `main` (or your default branch name).
4. Enable **Require a pull request before merging**.
5. Set **Required number of approvals** to at least `1`.
6. Enable **Dismiss stale pull request approvals when new commits are pushed**.
7. Click **Create** (or **Save changes**).

![Branch protection rule form showing required approvals setting](images/27-branch-protection-rule.png)

> [!NOTE]
> On GitHub Enterprise Server, branch protection rules and rulesets may be governed by an organisation-level policy. Check with your admin before creating repository-level rules — they may need to be applied at the org or enterprise level instead.

### Add a CODEOWNERS file (recommended)

A `CODEOWNERS` file automatically requests a review from a specific team or individual whenever a PR touches certain paths. This is useful when your workflow generates files (for example, updating a changelog or configuration file) that a specific team owns.

Create or edit `.github/CODEOWNERS`:

```
# Agentic workflow definitions require a workflow maintainer review
.github/workflows/  @your-org/workflow-maintainers

# Any AI-generated changelog updates require a release team review
CHANGELOG.md        @your-org/release-team
```

Replace `@your-org/workflow-maintainers` with a real GitHub team or username.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/` in your repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Name the file `CODEOWNERS`.
4. Paste the patterns above (edited for your org and team names).
5. Click **Commit new file**.

</details>

### Verify the protection is active

1. Trigger your agentic workflow manually (go to **Actions** → select your workflow → **Run workflow**).
2. When the workflow creates a pull request, open it.
3. Confirm that the PR is **blocked from merging** until a required reviewer approves it.
4. Approve the PR yourself (or ask a teammate), then merge it.

This confirms the full governance loop: the agent proposes a change, a human reviews it, and merge is gated on that approval.

### Enterprise: configure organisation-level Copilot policies

If you manage an organisation, you can control which repositories are allowed to run agentic workflows at all.

1. Go to your organisation's **Settings** → **Copilot** → **Policies**.
2. Under **GitHub Actions**, confirm that **Copilot in GitHub Actions** is enabled for the repositories that need it.
3. If you want to restrict agentic workflows to specific repositories, set the policy to **Selected repositories** and add your approved list.

> [!NOTE]
> On GitHub Enterprise Cloud and GHES, organisation admins can also set a `max-ai-credits` ceiling at the org level that overrides individual workflow caps. Ask your GitHub administrator whether an org-level cap is already in place before setting a lower cap in your workflow frontmatter.

## ✅ Checkpoint

- [ ] Your workflow's `permissions:` block uses `contents: read` rather than `contents: write` (unless the workflow truly needs to commit directly)
- [ ] Your default branch has a branch protection rule requiring at least one pull request approval
- [ ] You triggered your agentic workflow and confirmed that its PR cannot merge without a human review
- [ ] You created or reviewed a `CODEOWNERS` entry for `.github/workflows/`
- [ ] You can describe the difference between a branch protection rule and an organisation-level Copilot policy

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

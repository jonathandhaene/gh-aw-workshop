<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _As agentic workflows proliferate, your organisation needs consistent policies, guardrails, and an approval process — this step shows you how to set them up._

## 🎯 What You'll Do

You'll apply organisation-level controls to your agentic workflows: required reviewers for workflow file changes, repository rulesets that gate deployments, and a shared policy file that sets default limits for every team. By the end, your organisation has a repeatable governance baseline ready for enterprise rollout.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have repository **Admin** access or an organisation owner who can create rulesets.
- _(GHEC users)_ Confirm your organisation has Copilot Enterprise enabled before continuing.

## Steps

### Understand the governance surface

Three layers work together to keep agentic workflows safe at scale:

| Layer | What it controls |
|---|---|
| **Repository rulesets** | Who can push changes to workflow files |
| **Required reviewers** | Human sign-off before workflow changes merge |
| **Policy frontmatter defaults** | Shared cost caps, timeout limits, and permission floors |

Rulesets and required reviewers live in GitHub's repository or organisation settings. Policy defaults live in a checked-in configuration file that `gh aw compile` reads automatically.

> [!NOTE]
> For the complete reference, see [Governance guide](https://github.github.com/gh-aw/guides/governance/) in the gh-aw documentation.

### Protect workflow files with a ruleset

A **branch ruleset** prevents direct pushes to `.github/workflows/*.md` on your default branch.

**GitHub UI path (recommended):**

1. Open your repository on GitHub and click **Settings** → **Rules** → **Rulesets**.
2. Click **New ruleset** → **New branch ruleset**.
3. Name it `Protect agentic workflow files` and set **Enforcement status** to **Active**.
4. Under **Target branches**, click **Add target** → **Include default branch**.
5. Under **Restrict file paths**, add the pattern `.github/workflows/*.md`.
6. Enable **Require a pull request before merging** and set **Required approvals** to `1`.
7. Click **Create**.

<details>
<summary>🖥️ CLI alternative</summary>

Rulesets can also be created from the GitHub CLI with a JSON payload. See the [GitHub REST API docs for rulesets](https://docs.github.com/en/rest/repos/rules) for the full schema. For most teams, the UI path above is easier to maintain.

</details>

From now on, all changes to `.github/workflows/*.md` files must go through a pull request with at least one approving review before they can merge.

### Add a CODEOWNERS entry for workflow files

A `CODEOWNERS` file ensures the right team is automatically requested as a reviewer on every workflow PR.

1. In your repository on GitHub, navigate to `.github/CODEOWNERS` (create the file if it doesn't exist).
2. Click the **pencil icon (✏️)** to edit.
3. Add this line, replacing `@your-org/workflow-admins` with your real team:

```
.github/workflows/*.md @your-org/workflow-admins
```

4\. Click **Commit changes**.

> [!TIP]
> If you don't have a `workflow-admins` team yet, create one in **Your organisation** → **Teams** → **New team**. Add the engineers who own the agentic workflow catalogue.

### Create a shared policy defaults file

`gh aw` can read a policy file committed to your repository that sets default frontmatter values for every workflow that doesn't override them.

Create `.github/aw-policy.yml` with sensible defaults:

```yaml
defaults:
  timeout-minutes: 15
  max-ai-credits: 500
  max-daily-ai-credits: 2000
  permissions:
    contents: read
    issues: write
    pull-requests: write
```

Commit this file to your repository. When `gh aw compile` runs in CI, it merges these defaults with each workflow's frontmatter — individual workflows can override any field by declaring it explicitly.

> [!NOTE]
> The policy file applies at compile time, not at runtime. If you change it, recompile your workflows to regenerate the lock files.

### Verify governance is enforced

Test the ruleset by attempting a direct push to your default branch:

```bash
git checkout main
# Edit a workflow file
git add .github/workflows/daily-status.md
git commit -m "test: direct push to workflow"
git push origin main
```

GitHub should reject the push with a message like:

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
```

This confirms your ruleset is active. Open a pull request to make the change properly.

## ✅ Checkpoint

- [ ] You created a branch ruleset that restricts direct pushes to `.github/workflows/*.md`
- [ ] Pull request reviews are required before workflow file changes can merge
- [ ] You added a `CODEOWNERS` entry so the right team is auto-requested as reviewer
- [ ] You committed a `.github/aw-policy.yml` file with org-wide default limits
- [ ] You verified that a direct push to a protected workflow file is rejected

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

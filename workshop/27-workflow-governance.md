<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows

> _Enterprise teams don't just build workflows — they control who can create them, what they can do, and when they run._

## 🎯 What You'll Do

Apply governance controls to your agentic workflows. By the end of this step, you will have reviewed the key policy levers available at the repository and organisation level, applied required-reviewer settings to protect sensitive workflows, and understood how to scope workflow permissions to the minimum needed.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin or owner access to the repository you are using for this workshop, or you are working alongside someone who does.
- Optional: you are on GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) 3.12+. Some policy controls below are enterprise-only.

## Steps

### Understand the governance stack

Agentic workflows sit inside GitHub Actions, so every Actions governance control applies to them. On top of that, `gh-aw` adds its own permission layer through frontmatter.

There are three levels to control:

| Level | What it controls |
|-------|-----------------|
| **Organisation policy** | Which repositories can run Actions; which external actions are allowed |
| **Repository settings** | Who can approve and trigger workflow runs; required reviewers for deployments |
| **Workflow frontmatter** | Which GitHub API scopes the agent can use (`permissions:`) |

You control all three independently. Start at the outermost level and work inward.

### Audit your workflow permissions

Open `.github/workflows/daily-status.md` (or whichever workflow you built during this workshop). Find the `permissions:` block in the frontmatter. It should look something like this:

```yaml
permissions:
  contents: read
  issues: write
```

This is the **minimum-privilege principle** in action. Each permission grants the agent one scope. Remove any permission your workflow does not actually use.

If your workflow has no `permissions:` block, it inherits the repository default — usually `read` for all scopes. Add an explicit block to lock it down:

```yaml
permissions:
  contents: read
```

This tells the runner exactly what the agent may access. An agent that cannot write to `issues` cannot accidentally post unwanted comments.

### Apply branch protection and required reviewers

For workflows that write to your repository (creating issues, comments, or pull requests), protect the branch that hosts the workflow file:

1. Go to your repository on GitHub and open **Settings** → **Branches**.
2. Click **Add branch protection rule** (or edit an existing rule for `main`).
3. Enable **Require a pull request before merging** and set at least one required reviewer.
4. Optionally enable **Require review from Code Owners** if you have a `CODEOWNERS` file.

This ensures no one — including an automated agent — can silently change workflow logic. Every edit goes through a human review.

<details>
<summary>🏢 Enterprise: organisation-level policy controls</summary>

On GHEC and GHES you can restrict Actions at the organisation level:

1. Go to your organisation settings and open **Actions** → **General**.
2. Under **Policies**, choose **Allow select actions and reusable workflows**.
3. Add the `gh-aw` action's owner (for example `github`) to the allowlist.

This prevents repositories from pulling in arbitrary third-party actions — only explicitly approved actions and workflows can run.

</details>

### Add a deployment environment for sensitive workflows

GitHub Actions **Environments** let you require manual approval before a workflow run can proceed. This is useful when a workflow writes to production systems or external services.

1. Go to **Settings** → **Environments** → **New environment**.
2. Name it (for example `agentic-production`) and click **Configure environment**.
3. Enable **Required reviewers** and add the reviewers you want to approve runs.
4. In your workflow frontmatter, reference the environment:

```yaml
jobs:
  run:
    runs-on: ubuntu-latest
    environment: agentic-production
```

Any run that targets this environment will pause and wait for an approver before the agent step executes.

> [!NOTE]
> Environments are available on all public repositories and on private repositories with GitHub Pro, Team, or Enterprise plans.

### Review the gh-aw governance guide

The `gh-aw` documentation includes a dedicated governance reference with org-level controls, runner scoping, and Copilot policy settings for enterprise admins:

- [Governance guide](https://github.github.com/gh-aw/guides/governance/) — recommended controls for organisation owners rolling out agentic workflows at scale.

Bookmark this page and share it with your GitHub admin if you are deploying workflows to a shared organisation.

## ✅ Checkpoint

- [ ] Your workflow frontmatter has an explicit `permissions:` block listing only the scopes it needs
- [ ] You can describe the difference between organisation-level Actions policy and workflow-level permissions
- [ ] You have reviewed (or set up) branch protection on the branch that hosts your workflow files
- [ ] You know how to create a deployment environment and require manual approval before a run proceeds
- [ ] You bookmarked the gh-aw governance guide to share with your admin

<!-- journey: all -->
Want to explore more? Return to [What's Next? Keep Exploring](14-next-steps.md) to choose another path.
<!-- /journey -->

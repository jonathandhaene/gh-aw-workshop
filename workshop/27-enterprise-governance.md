<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at Scale

> _One well-designed workflow is a personal win — organisation-wide governance is what turns it into a durable, trustworthy asset for your whole team._

## 🎯 What You'll Do

You will apply organisation-level controls to your agentic workflows: set reusable policies, restrict which workflows can run and where, and establish a lightweight review gate before any new agentic workflow reaches production. By the end, you will know where each control lives and how to verify it is active.

## 📋 Before You Start

- You have a cost-managed workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** or **Admin** access to a GitHub organisation, _or_ you can read along and apply the steps to a personal repository to understand the concepts.
- If you are on GitHub Enterprise Server (GHES), confirm you are on **3.12 or later** — earlier versions do not support Copilot-powered agentic workflows. See [Enterprise Setup](side-quest-enterprise-setup.md) if you are unsure.

## Steps

### Review your organisation's Actions policy

Organisation owners control which Actions workflows are allowed to run.

1. In your organisation, go to **Settings** → **Actions** → **General**.
2. Under **Actions permissions**, check whether workflows are restricted to actions from trusted sources only.
3. Confirm that agentic workflows (which run as standard GitHub Actions jobs) are permitted under the current policy.

> [!NOTE]
> If your organisation already restricts forks or third-party actions, agentic workflows authored inside the organisation are unaffected — they run as first-party workflows from your own repository.

### Set a required workflow for pull-request review

Before a new agentic workflow can merge to the default branch, you can require a review step using **required status checks** or **required reviewers** on branch protection rules.

1. Go to the repository that will host agentic workflows → **Settings** → **Branches** → **Add branch protection rule** (for `main`).
2. Enable **Require a pull request before merging** and set a minimum of **one reviewer**.
3. Optionally enable **Require status checks to pass** and add a validation job — for example, a job that runs `gh aw compile --validate` on changed workflow files.

This ensures every new or updated agentic workflow has at least one human approval before it can run on a schedule or trigger.

<details>
<summary>🖥️ Setting up the validation check via the GitHub UI</summary>

1. Create a file `.github/workflows/validate-aw.yml` in your repository.
2. Add a simple job that runs `gh aw compile --validate` against any `*.md` file changed in the pull request.
3. Commit and push the file — it becomes available as a status check.
4. Return to **Settings** → **Branches** → edit your protection rule → search for `validate-aw` under **Require status checks to pass before merging** → save.

</details>

### Add organisation-level secrets for shared credentials

When multiple repositories need the same API key, store it once at the organisation level.

1. Go to your **organisation** → **Settings** → **Secrets and variables** → **Actions**.
2. Click **New organisation secret**, name it (e.g., `SHARED_API_KEY`), and paste the value.
3. Under **Repository access**, choose **Selected repositories** and add only the repositories that need it.

Reference it in your workflow frontmatter the same way you would a repository secret:

```yaml
env:
  SHARED_API_KEY: ${{ secrets.SHARED_API_KEY }}
```

> [!TIP]
> Rotate organisation secrets on the same cycle as your other credentials — quarterly is a common baseline.

### Document your agentic workflow inventory

Maintain a lightweight registry of every agentic workflow in your organisation — a Markdown table in a shared repository is enough. Include the workflow name, repository, trigger, owner, and last review date. This helps you spot stale or duplicate workflows quickly. See the [governance guide](https://github.github.com/gh-aw/guides/governance/) for a fuller template.

### Confirm GHEC-specific Copilot policy (GHEC learners only)

If your organisation is on **GitHub Enterprise Cloud**, Copilot access for GitHub Actions is controlled by an enterprise policy:

1. Enterprise owner goes to **Enterprise** → **Policies** → **Copilot** → **Copilot in GitHub Actions**.
2. Confirm the policy is set to **Allowed** (or the subset that includes your organisation).
3. Individual repository owners must also enable **Copilot** under **Settings** → **Copilot** if the enterprise policy delegates the decision to repositories.

> [!NOTE]
> Organisation-level Copilot billing must be active for agentic workflows to use Copilot as the engine. If runs fail with a model-access error, re-check this policy before debugging the workflow itself.

## ✅ Checkpoint

- [ ] You reviewed your organisation's Actions permissions policy and confirmed agentic workflows are allowed to run
- [ ] You added or verified a branch protection rule on `main` that requires at least one reviewer before merging workflow changes
- [ ] You can explain why storing shared credentials as organisation secrets is safer than duplicating them across repositories
- [ ] You created or planned an agentic workflow inventory for your team
- [ ] If you are on GHEC, you confirmed that the enterprise Copilot policy permits Copilot in GitHub Actions

<!-- journey: all -->
Want to explore another path? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

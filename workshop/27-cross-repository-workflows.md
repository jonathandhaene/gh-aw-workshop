<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Act Across Multiple Repositories

> _The most powerful agentic workflows reach beyond a single repository — coordinating changes, collecting signals, and taking action across your whole organisation._

## 🎯 What You'll Do

You'll extend your daily-status workflow to read from a second repository, then write a brief that acts on data it found there. By the end you'll understand how cross-repository access works, which permissions it needs, and how to keep it safe.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your `daily-status` workflow runs successfully and you can edit its frontmatter.
- You have access to at least one other repository (it can be a public repo or any repo your account can read).
- `gh aw` is installed and authenticated (see [Install the gh-aw CLI Extension](06-install-gh-aw.md)).

## Steps

### Understand how cross-repository access works

By default, an agentic workflow runs with a `GITHUB_TOKEN` scoped to the repository that owns the workflow file. That token can read and write _only_ that repository.

To reach a second repository you have two options:

| Option | When to use it |
|---|---|
| **`contents: read` + GitHub Tools** | Read-only access to another repo you own or that is public — no extra secrets needed |
| **Fine-grained Personal Access Token (PAT) stored as a secret** | Write access to another repo, or any private repo outside your own account |

> [!NOTE]
> The [cross-repository reference docs](https://github.github.com/gh-aw/reference/cross-repository/) cover additional patterns including GitHub App tokens for enterprise-scale multi-repo orchestration.

### Declare cross-repository permissions in frontmatter

Open your `daily-status.md` workflow file. Find the `permissions:` block and add `contents: read` if it isn't already there:

```yaml
---
name: Daily Status
on:
  schedule:
    - cron: "0 8 * * 1-5"
  workflow_dispatch:

permissions:
  contents: write
  issues: write

tools:
  github:
    - cross_repository_read
---
```

The `cross_repository_read` tool gives the agent read access to public repositories and repositories owned by the same account. Adding it to the `tools.github` list makes it available in the agent session without additional secrets.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your `my-agentic-workflows` repository on GitHub, navigate to `.github/workflows/daily-status.md`.
2. Click the pencil icon (✏️) to edit the file.
3. Add `cross_repository_read` under `tools.github:` as shown above.
4. Click **Commit changes** and commit directly to `main`.

</details>

### Update the agent brief to use a second repository

Below the frontmatter, update your task brief to instruct the agent to fetch data from a second repository. Replace `<owner>/<other-repo>` with a real repository path:

```markdown
## Task

1. Fetch the five most recently closed issues from `<owner>/<other-repo>` using the cross_repository_read tool.
2. Fetch any open pull requests from that same repository that have been open more than seven days.
3. Summarise findings in a daily-status issue in *this* repository, combining the external data with the usual local status.
```

> [!TIP]
> Keep the brief concrete. Tell the agent which repository to read, what data to fetch, and where to write the result. Vague cross-repo instructions are a common cause of runaway tool calls and higher AIC costs.

### Compile and run

After saving, compile locally to catch any frontmatter errors:

```bash
gh aw compile daily-status
```

Then trigger a manual run from the **Actions** tab: select your **Daily Status** workflow, click **Run workflow**, and watch the logs.

When the run completes, open the new issue it created. You should see a section sourced from the second repository.

### Use a PAT secret for private or write access

If the second repository is private or you need to write to it, create a [fine-grained PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) with the minimum required scopes, then store it as a repository secret named `CROSS_REPO_TOKEN`.

Reference it in your frontmatter:

```yaml
env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
```

The agent automatically uses `CROSS_REPO_TOKEN` when it is present and the target repository requires authentication.

> [!NOTE]
> For enterprise deployments (GHES/GHEC), prefer a **GitHub App** over a PAT for production cross-repository workflows. GitHub Apps have explicit installation scopes, automatic token rotation, and a clear audit trail — all requirements in most enterprise governance policies. See [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md) for a starting point.

## ✅ Checkpoint

- [ ] You added `cross_repository_read` to your workflow's `tools.github` list
- [ ] Your task brief names a specific second repository and describes what data to fetch from it
- [ ] `gh aw compile daily-status` completed without errors
- [ ] A manual workflow run succeeded and the resulting issue includes data from the second repository
- [ ] You can explain the difference between `cross_repository_read` and a PAT-backed secret for cross-repo access
- [ ] You know when a GitHub App token is preferred over a PAT in enterprise environments

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

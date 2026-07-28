<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Orchestrate Workflows Across Multiple Repositories

> _Real-world organisations spread their code across dozens of repositories — agentic workflows can bridge those boundaries so one trigger produces coordinated action everywhere it matters._

## 🎯 What You'll Do

You'll update an existing agentic workflow to read data from a second repository, understand the permissions model that makes cross-repository access safe, and confirm that the workflow run successfully fetches and cites that external data.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow runs at least once without errors.
- You have read access to at least one other repository in your organisation (or a public repository you can use for practice).
- _(Enterprise users)_ Your GitHub administrator has confirmed that fine-grained PATs or GitHub App tokens are available for cross-repository access.

## Steps

### Understand the cross-repository model

An agentic workflow runs inside a single repository, but the Copilot agent can read other repositories if:

1. The workflow's **`permissions`** block includes `contents: read` (grants access to the host repo).
2. A **token with cross-repository scope** is passed as a secret — for example, a fine-grained PAT scoped to the repositories you need.

> [!NOTE]
> By default, `GITHUB_TOKEN` is scoped to the repository that owns the workflow. It cannot read private data in other repositories. You must supply an additional token for those reads.

For public repositories, the agent can fetch data using the GitHub API without any extra token. This is a great place to start practising.

### Add a cross-repository data step to your workflow

Open your workflow `.md` file and add a new step to the `steps` block that fetches data from a second repository. Here is a minimal example using a public repository:

```yaml
---
name: cross-repo-status
on:
  workflow_dispatch:
permissions:
  contents: read
  issues: write
---
```

The task brief then instructs the agent what to do with that cross-repository data:

```
Fetch the last five closed issues from the repository `github/docs` using
the GitHub API. Summarise the topics in three bullet points and open a new
issue in this repository titled "Upstream docs summary" with that summary
as the body.
```

> [!TIP]
> The agent uses its built-in GitHub tools to call the API. You do not need to write shell commands — just describe what data to fetch and what to do with it.

### Grant access for private repositories

If the target repository is **private**, you need an additional secret:

1. Create a fine-grained PAT in **Settings → Developer settings → Personal access tokens → Fine-grained tokens** with **Contents: Read** on the repositories you need.
2. Add it as a repository secret named `CROSS_REPO_TOKEN` in **Settings → Secrets and variables → Actions → New repository secret**.
3. Reference it in your workflow frontmatter:

```yaml
---
name: cross-repo-private
on:
  workflow_dispatch:
permissions:
  contents: read
  issues: write
env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
---
```

In the task brief, tell the agent to use this token when calling the GitHub API for that repository.

> [!NOTE]
> For GitHub Enterprise Server or GitHub Enterprise Cloud, fine-grained PATs must be enabled by an organisation owner before members can create them. Check with your administrator if the option is missing.

### Commit and run

<details>
<summary>🖥️ GitHub UI path</summary>

1. Open your workflow `.md` file in GitHub and click the **pencil icon (✏️)**.
2. Update the frontmatter and task brief as shown above.
3. Click **Commit changes**, then go to **Actions → your workflow → Run workflow**.

</details>

If you prefer the terminal, save your edits, compile, and push:

```bash
gh aw compile
git add .github/workflows/your-workflow.md .github/workflows/your-workflow.lock.yml
git commit -m "Add cross-repository data fetch"
git push
```

### Verify the run

1. Go to **Actions** and open the latest run.
2. Confirm the agent log shows a GitHub API call to the second repository.
3. Check that the new issue was created in your repository with the expected summary.

![Cross-repository workflow run log showing an API call to a second repository](images/27-cross-repo-run.png)

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes `contents: read` in the `permissions` block
- [ ] Your task brief instructs the agent to fetch data from a second repository
- [ ] The workflow run completed without a permissions error
- [ ] The agent log shows a successful API call to the external repository
- [ ] A new issue was created in your repository summarising the cross-repository data
- [ ] You can explain the difference between `GITHUB_TOKEN` scope and a fine-grained PAT

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

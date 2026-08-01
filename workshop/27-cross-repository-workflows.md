# Run Workflows Across Multiple Repositories

> _Most real work happens across many repos — your agentic workflows can reach all of them._

## 🎯 What You'll Do

Add cross-repository read access to an existing agentic workflow so it can query data from any repository in your organisation, then write a brief that summarises activity across multiple repos in a single run.

## 📋 Before You Start

- You have a working agentic workflow from [Make Your Workflow Smarter with Conditional Logic](15-conditional-logic.md) or any earlier step.
- Your workflow runs successfully in GitHub Actions.

## Steps

### Understand why cross-repository access works differently

By default, every workflow run receives a short-lived `GITHUB_TOKEN` scoped to the repository where the workflow lives. To read _other_ repositories, you grant the token additional scope with the `permissions:` block — or, for repos outside your org, you use a separate credential stored as a secret.

> [!NOTE]
> `contents: read` in `permissions:` covers the current repository only. For org-wide access, the `GITHUB_TOKEN` must belong to an installation that has been granted access to each target repository.

### Grant the workflow organisation-level read access

Open your workflow `.md` file and add (or extend) the `permissions:` block:

```yaml
permissions:
  contents: read
  pull-requests: read
  issues: read
  metadata: read
```

`metadata: read` is required for any cross-repository lookup on GitHub.com and GitHub Enterprise Cloud.

### List the repositories you want to query

In your workflow frontmatter, add a `with:` block that names the target repositories:

```yaml
with:
  repositories: |
    your-org/repo-alpha
    your-org/repo-beta
    your-org/repo-gamma
```

Your agent brief can then iterate over this list with a templating expression:

```
For each repository listed in {{ inputs.repositories }}, fetch the five most recently updated open issues and summarise them in a bullet list grouped by repository.
```

### Write the cross-repository brief

Add a task brief that uses the GitHub tools to loop over repositories:

```
You are a daily cross-repository reporter.

1. For each repository in {{ inputs.repositories }}:
   a. List open pull requests updated in the last 24 hours.
   b. List open issues labelled "needs-attention" or "bug".
2. Produce a Markdown summary table: | Repository | Open PRs | Flagged Issues |
3. Post the table as a new issue comment on the tracking issue in
   {{ env.TRACKER_REPO }} using the `create_issue_comment` tool.
```

### Commit and run

<details>
<summary>🖥️ GitHub UI path</summary>

1. Navigate to your workflow file in your repository on GitHub.
2. Click the **pencil icon (✏️)** to edit it.
3. Paste in the updated `permissions:` block and `with:` entries.
4. Click **Commit changes**.
5. Go to **Actions → your workflow → Run workflow** to trigger a manual run.

</details>

In a terminal, commit and trigger a run:

```bash
git add .github/workflows/your-workflow.md
git commit -m "feat: add cross-repository read access"
git push
gh workflow run your-workflow.md
```

### Read the multi-repo summary

Open the run in **Actions** and expand the agent step. You should see tool calls for each repository — the agent fetches pull requests and issues from every repo you listed, then assembles the summary table.

> [!TIP]
> If the agent reports `403 Resource not accessible` for a repository, that repo has not been granted access to this workflow's installation. Ask an organisation admin to add it under **Settings → Actions → General → Repository access**.

## ✅ Checkpoint

- [ ] Your workflow `permissions:` block includes `metadata: read`
- [ ] You added at least two repositories to the `with: repositories` list
- [ ] A manual run completed without permission errors
- [ ] The agent log shows tool calls for each target repository
- [ ] The final summary table lists data from more than one repository

<!-- journey: all -->
**Next:** [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md)
<!-- /journey -->

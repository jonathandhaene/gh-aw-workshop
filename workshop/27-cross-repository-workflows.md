# Orchestrate Workflows Across Multiple Repositories

> _One agent, many repositories — learn how to read, write, and coordinate across your entire organisation from a single workflow._

## 🎯 What You'll Do

You will extend a workflow to act across more than one repository. By the end of this step you will have a workflow that reads data from a second repository and writes a summary back to the first — the foundation of org-wide automation and enterprise reporting.

## 📋 Before You Start

- You have a working agentic workflow from [Test and Improve Your Workflow](12-test-and-iterate.md) or later steps.
- You understand how [`permissions:`](https://github.github.com/gh-aw/reference/permissions/) and [`safe-outputs:`](https://github.github.com/gh-aw/reference/safe-outputs/) interact from [Make Your Workflows Resilient to Failure](22-error-handling-and-resilience.md).
- Your workflow is committed and has run successfully at least once.

> [!NOTE]
> Enterprise users on GHES or GHEC: cross-repository access follows the same token model as github.com, but your enterprise base URL differs. Replace `github.com` references with your enterprise URL wherever it appears in API calls or `network.allowed-domains`.

## Steps

### Understand the token model

The default `GITHUB_TOKEN` is scoped to the **repository that contains the workflow**. To read or write a _different_ repository you need a token that covers it.

Two approaches exist:

| Approach | When to use |
|----------|-------------|
| GitHub App token (recommended) | Organisation-wide automation; fine-grained control per repo |
| Personal access token (PAT) stored as a secret | Quick setup for personal projects or small teams |

For this workshop you will use a fine-grained PAT stored as a repository secret.

### Create a fine-grained PAT

1. Go to **Settings → Developer settings → Personal access tokens → Fine-grained tokens** on GitHub.
2. Click **Generate new token**.
3. Set an **Expiration** (30 days is reasonable for learning).
4. Under **Repository access**, choose **Only select repositories** and add both the source repository you want to read and your workflow repository.
5. Under **Permissions**, grant **Contents: Read** for the source repository and **Issues: Write** for the workflow repository.
6. Click **Generate token** and copy it immediately.

### Store the PAT as a secret

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your workflow repository on GitHub.
2. Click **Settings → Secrets and variables → Actions**.
3. Click **New repository secret**.
4. Name it `CROSS_REPO_TOKEN` and paste the PAT value.
5. Click **Add secret**.

</details>

```bash
gh secret set CROSS_REPO_TOKEN --body "<paste-token-here>"
```

### Update your workflow frontmatter

Open your workflow `.md` file and update the frontmatter to declare the secret and add cross-repository network access:

```yaml
---
name: Cross-Repository Status Summary
on:
  schedule: daily at 09:00

permissions:
  contents: read
  issues: write

env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
  SOURCE_REPO: "your-org/source-repo"

tools:
  github:
    read_issues: true
    read_pull_requests: true
    create_issue_comment: true
---
```

> [!TIP]
> The [`cross-repository`](https://github.github.com/gh-aw/reference/cross-repository/) reference page explains the full token and permission model for multi-repo workflows.

### Write the agent brief

Below the frontmatter, add a Markdown task brief that tells the agent what to do across repositories:

```markdown
You are an org-wide status reporter. Your job:

1. Using the CROSS_REPO_TOKEN, list the 5 most recently updated open issues in
   the SOURCE_REPO repository.
2. For each issue: note the title, author, and days since last update.
3. Identify any issue that has had no activity in more than 7 days — flag it as
   "stale".
4. Post a concise summary comment on issue #1 in THIS repository listing:
   - Total open issues reviewed
   - Number of stale issues
   - Top 3 most recently active issues (title and link)

Keep the comment under 300 words. Use a markdown table for the stale list.
```

### Commit and run

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to the workflow file in your repository and click the **pencil icon (✏️)**.
2. Make your edits in the browser editor.
3. Click **Commit changes** to save directly on your branch.
4. Go to **Actions** and trigger the workflow manually using **Run workflow**.

</details>

Save your edits locally, then push and trigger the run:

```bash
gh aw compile
git add .github/workflows/cross-repo-status.md .github/workflows/cross-repo-status.lock.yml
git commit -m "Add cross-repository status workflow"
git push
gh workflow run cross-repo-status.md
```

Watch the log in **Actions**. Look for tool calls that reference the source repository — you should see the agent reading issues under a different `repo:` value than your workflow repository.

### Read the output

Once the run completes, open issue #1 in your workflow repository. You should see a new comment with the summary table. If the run fails, check the [Debugging Checklist](side-quest-09-01f-debugging-checklist.md).

## ✅ Checkpoint

- [ ] You created a fine-grained PAT scoped to at least two repositories
- [ ] You stored the PAT as a repository secret named `CROSS_REPO_TOKEN`
- [ ] Your workflow frontmatter declares the `env:` block referencing the secret
- [ ] Your agent brief instructs the agent to read from a second repository and write to the first
- [ ] The workflow ran successfully and posted a comment to issue #1
- [ ] You can see the cross-repository tool calls (different `repo:` value) in the Actions log

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

# Read Data from Another Repository

> _Your workflows don't have to stay inside a single repository — enterprise teams often need to aggregate data, audit multiple projects, or read from a shared configuration repository._

## 🎯 What You'll Do

You'll extend your agentic workflow to read from a second repository in the same organization. By the end of this step, your workflow will pull data from a target repository and include it in its output — without needing to clone it locally.

## 📋 Before You Start

- Completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) or [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md)
- Your workflow runs successfully on a schedule (from [Run and Watch Your Workflow](08-run-your-workflow.md))
- You have at least one other repository in your account (a fork, practice repo, or a new empty one is fine)

## Steps

### Understand what "cross-repository" means

When your agentic workflow runs, it has access to the repository it lives in by default. To read from a _different_ repository, you need to:

1. Grant the workflow read access to that repository using the `permissions:` block.
2. Tell the agent which repository to read from in the task brief.

The agent uses GitHub's built-in tools (such as `list_issues`, `get_file_contents`, and `list_pull_requests`) to fetch data from any repository the workflow token can access.

> [!NOTE]
> Cross-repository _reads_ are safe and don't require write permissions on the target repository. Cross-repository _writes_ need extra care — you must explicitly allow them in `safe-outputs:` and in the target repository's settings.

### Pick a target repository

You need a second repository to read from. Use any repository you own or have read access to — your fork of this workshop, a practice project, or a fresh empty repository.

If you need a quick target, create a new repository at [github.com/new](https://github.com/new):

- Name it something like `my-status-target`
- Initialize it with a README
- Keep it public or private — either works for this exercise

Note the full name: `your-username/repository-name`. You'll use it shortly.

### Update your workflow frontmatter

Open your `daily-report-status.md` workflow file (in `.github/workflows/`).

__GitHub UI path (recommended):__

1. Navigate to `.github/workflows/daily-report-status.md` in your repository.
2. Click the __pencil icon (✏️)__ to edit.
3. Update the `permissions:` block to include `contents: read` (you may already have this).
4. Click __Commit changes__ when done.

<details>
<summary>🖥️ Example: updated frontmatter with cross-repository read permission</summary>

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: read
  pull-requests: read
---
```

The `contents: read` permission lets the workflow token read file contents from repositories in the same organization. For repositories in _other_ organizations, you may need a `GITHUB_TOKEN` from that organization or a personal access token stored as a secret.

</details>

### Update the task brief

After the closing `---` of your frontmatter, update the Markdown body to instruct the agent to gather data from the second repository.

Here is an example task brief you can adapt:

```markdown
## Task

Fetch the 5 most recent open issues from `your-username/repository-name`.
For each issue, include: title, author, date opened, and label names.
Post a short summary as a comment on the issue or write it to a file called `cross-repo-report.md` at the root of this repository.
```

Replace `your-username/repository-name` with your actual target repository name.

> [!TIP]
> Be specific about _what_ to read and _where_ to write the result. The agent follows your brief closely — a vague instruction like "check the other repo" will produce inconsistent output.

### Compile and trigger a run

After saving your changes, compile the workflow to regenerate the lock file:

```bash
gh aw compile
```

If you prefer not to use the terminal, push your edits and let the next scheduled trigger pick up the compiled lock file automatically (the Actions runner compiles on each run when no lock file is present).

Then trigger a manual run to test right away:

1. Go to __Actions__ in your repository.
2. Select __Daily Status Report__.
3. Click __Run workflow__ → __Run workflow__.

Watch the run log to see the agent fetch data from the second repository.

### Check the output

After the run completes, look for:

- A new `cross-repo-report.md` file in your repository root (if you asked the agent to write one), or
- A comment posted on a relevant issue.

If the agent couldn't access the target repository, the log will show a `permission denied` or `not found` error. Check that your `permissions:` block includes `contents: read` and that the target repository is accessible to your account.

> [!NOTE]
> __Enterprise users:__ If the target repository is in a different organization, the built-in `GITHUB_TOKEN` may not have cross-organization access. Store a personal access token (PAT) with `repo:read` scope as a repository secret and reference it in your workflow. See [Storing Credentials with GitHub Secrets](side-quest-16-02-secrets-and-permissions.md) for setup instructions.

## ✅ Checkpoint

- [ ] You identified a target repository to read from and noted its full name (`owner/repo`)
- [ ] You updated your workflow's `permissions:` block to include `contents: read`
- [ ] You updated the task brief to instruct the agent to fetch data from the target repository
- [ ] You triggered a manual workflow run and watched the log
- [ ] The agent successfully read data from the target repository and produced output
- [ ] You can explain the difference between same-organization and cross-organization access for the workflow token

__Next:__ [What's Next? Keep Exploring](14-next-steps.md)

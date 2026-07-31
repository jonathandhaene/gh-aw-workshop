<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Read Data Across Multiple Repositories

> _A single workflow that spans several repositories unlocks fleet-level insights no single-repo report can match._

## 🎯 What You'll Do

You will extend an agentic workflow to read data from two or more repositories and produce a consolidated summary. By the end you will have a working multi-repo workflow and understand the permissions required to run it in an enterprise environment.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) or [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow already runs successfully against one repository.
- You have write access to at least one other repository you want to read from (or you can create a second test repository).

## Steps

### Understand the permissions model

A gh-aw workflow runs as a GitHub Actions job. By default, the `GITHUB_TOKEN` it receives is scoped to the **repository that hosts the workflow**. To read data from other repositories, you need a broader credential.

Two common options:

| Approach | Best for | Scope |
|----------|----------|-------|
| GitHub App installation token | Enterprise and team use | Fine-grained, auditable |
| Classic PAT stored as a secret | Quick personal experiments | Broad — use with care |

> [!TIP]
> For enterprise environments, a **GitHub App** is the preferred approach. It allows your security team to audit exactly which repositories the workflow can access and revoke access without touching developer credentials.

### Store a cross-repository token as a secret

In your workflow repository on GitHub, go to **Settings** → **Secrets and variables** → **Actions** → **New repository secret**. Name it `CROSS_REPO_TOKEN` and paste your PAT or App installation token.

### Add the token to your workflow frontmatter

Open your workflow `.md` file and add the secret as an environment variable.

```yaml
---
name: Multi-Repo Status Report
on:
  workflow_dispatch:
env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
permissions:
  contents: read
  issues: write
---
```

> [!NOTE]
> Keep `permissions` as narrow as possible. `contents: read` on the workflow repository plus the cross-repo token covers most read-only reporting use cases.

### Update the agent brief

Add instructions that tell the agent which repositories to query and what to do with the combined data.

````markdown
You are a multi-repository status reporter.

Repositories to summarise:
- {{ github.repository }}
- owner/aw-data-source

For each repository, report: open issue and PR count, the most recent commit message and author, and any workflow runs that failed in the last 24 hours.

Use the CROSS_REPO_TOKEN environment variable when making API calls to repositories other than {{ github.repository }}.

Write a consolidated Markdown summary and post it as an issue in {{ github.repository }}.
````

### Compile and push

Save your workflow file, then compile and push.

```bash
gh aw compile
git add .github/workflows/multi-repo-status.md .github/workflows/multi-repo-status.lock.yml
git commit -m "feat: add cross-repository status report workflow"
git push
```

If you prefer not to use the terminal, commit the `.md` file via the GitHub UI and run `gh aw compile` inside a Codespace afterward to regenerate the lock file.

### Run and verify

1. Go to **Actions** → select your workflow → click **Run workflow**.
2. When the run completes, open the generated issue.
3. Confirm the summary includes data from both repositories.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes the `CROSS_REPO_TOKEN` environment variable
- [ ] The `CROSS_REPO_TOKEN` secret is stored in your repository settings
- [ ] The agent brief lists at least two repositories and what to report for each
- [ ] A manual run completed without credential errors
- [ ] The generated summary issue contains data drawn from more than one repository
- [ ] You can explain why a GitHub App token is preferred over a PAT in enterprise settings

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose another advanced topic.
<!-- /journey -->

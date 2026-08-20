# Cross-Repository Workflows

> _A workflow that spans multiple repositories gives your team a single pane of glass — one scheduled run that reads from many repos and writes back to one authoritative place._

## 🎯 What You'll Do

Extend your daily-status workflow so it aggregates data from more than one repository. By the end, one scheduled workflow will pull open issues, recent commits, or pull request counts from two or more repos and write a combined summary to your home repository.

## 📋 Before You Start

- You completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or [What's Next? Keep Exploring](14-next-steps.md).
- Your `daily-status.md` workflow is committed and has run at least once.
- You have read access to at least one other repository you want to include (it can be a public repo).

> [!NOTE]
> Enterprise learners on GHES or GHEC: cross-repository access is controlled by **fine-grained personal access tokens** or **GitHub App installations**. The `GITHUB_TOKEN` minted for a workflow only has access to the repository that hosts the workflow. See the note under "Add repository scope" for your options.

## Steps

### Understand the scope boundary

Every agentic workflow runs with a `GITHUB_TOKEN` scoped to the **host** repository — the one that contains the `.github/workflows/` folder. To read data from a second repo you need to give your agent a credential with broader scope.

Choose one:

| Approach | When to use |
|----------|-------------|
| Personal access token (PAT) with repo read | Personal or team repos you own |
| GitHub App installation token | Enterprise orgs managing many repos |
| Public repo via `gh api` (no extra auth) | Public repos only |

This step uses a **fine-grained PAT** because it works for all account types. Enterprise learners may prefer a GitHub App; the [gh-aw cross-repository reference](https://github.github.com/gh-aw/reference/cross-repository/) describes both.

### Create a fine-grained PAT

1. Open [github.com/settings/tokens](https://github.com/settings/tokens) and click **Generate new token (fine-grained)**.
2. Set the **Resource owner** to your account (or your org, for enterprise).
3. Under **Repository access**, select **Only select repositories** and add every repo you want to read.
4. Under **Permissions → Repository permissions**, set **Contents** to **Read-only** and **Issues** to **Read-only**.
5. Click **Generate token** and copy the value immediately.

### Store the PAT as a secret

1. Open your practice repository on GitHub.
2. Go to **Settings → Secrets and variables → Actions → New repository secret**.
3. Name it `CROSS_REPO_TOKEN` and paste the token value.
4. Click **Add secret**.

<details>
<summary>🖥️ Terminal path — store secret with the gh CLI</summary>

```bash
gh secret set CROSS_REPO_TOKEN --repo your-username/my-agentic-workflows
```

Paste the token value when prompted. The CLI stores it encrypted, same as the UI.

</details>

### Update the workflow frontmatter

Open `.github/workflows/daily-status.md` in your repository.

Add `CROSS_REPO_TOKEN` to the `env:` block and extend `permissions:` to allow reading cross-repo data. Here is a minimal diff:

```yaml
---
name: daily-status
on:
  schedule: daily on weekdays
  workflow_dispatch: {}
permissions:
  issues: write
  contents: read
env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
tools:
  github: default
safe-outputs:
  create-issue: limit 1
---
```

> [!NOTE]
> `gh-aw` injects `CROSS_REPO_TOKEN` into the agent session automatically once it appears in `env:`. Your agent task brief can then instruct the agent to use it when calling the GitHub API for the target repos.

### Update the agent task brief

In the Markdown body of `daily-status.md`, add a paragraph that tells the agent which extra repositories to read and what data to collect. For example:

```markdown
## Additional context

For each repository listed below, collect the count of open issues and
the title of the most recent merged pull request. Use the
`CROSS_REPO_TOKEN` environment variable as the Bearer token when calling
the GitHub API. Do not include raw token values in any output.

Repositories to include:
- your-org/repo-alpha
- your-org/repo-beta
```

Replace `your-org/repo-alpha` and `your-org/repo-beta` with real repository names.

### Compile and push

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "feat: cross-repository aggregation in daily-status"
git push
```

<details>
<summary>🖥️ GitHub UI alternative (no terminal)</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to edit.
3. Make the frontmatter and brief changes described above.
4. Click **Commit changes**.
5. GitHub Actions will compile the lock file automatically on next run if `gh aw compile` is part of your workflow setup — or trigger a manual run to verify.

UI-first learners: you can skip the local `gh aw compile` step. Run the workflow from **Actions → Run workflow** and look for compile errors in the first job step.

</details>

### Trigger a test run

1. Go to **Actions** in your repository.
2. Select **daily-status** and click **Run workflow**.
3. Open the run log. Look for the agent calling the GitHub API for each extra repository.
4. Confirm the output summary mentions data from all repos you configured.

## ✅ Checkpoint

- [ ] You created a fine-grained PAT with read access to at least one additional repository
- [ ] You stored the PAT as a repository secret named `CROSS_REPO_TOKEN`
- [ ] You added `CROSS_REPO_TOKEN` to the `env:` block in your workflow frontmatter
- [ ] Your agent task brief names the additional repositories and what data to collect
- [ ] `gh aw compile` completed without errors (or GitHub Actions compiled on run)
- [ ] A manual run completed and the output summary includes data from more than one repository
- [ ] You can explain why `GITHUB_TOKEN` alone cannot access other repositories

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

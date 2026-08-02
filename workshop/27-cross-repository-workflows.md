# Cross-Repository Agentic Workflows

> _Most real engineering work spans more than one repository — this step shows you how to build agentic workflows that reach across that boundary._

## 🎯 What You'll Do

You will extend an agentic workflow so it reads data from a second repository, then writes a summary back to your primary one. By the end, you will understand how cross-repository access is authorised, scoped, and kept safe in the gh-aw model.

## 📋 Before You Start

- You have a working scheduled workflow from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have explored [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a second GitHub repository available to read from (it can be any public repository or another repo you own).

> [!NOTE]
> On **GitHub Enterprise Server (GHES)** or **GitHub Enterprise Cloud (GHEC)**, cross-repository workflows often require extra token scoping or approval from your org admin. Check with your GitHub admin before creating access tokens for repositories in a managed organisation.

## Steps

### Understand what "cross-repository" means

By default, a workflow's `GITHUB_TOKEN` only has permission to act on the repository that owns it. Reaching a second repository requires one of two approaches:

- **A fine-grained personal access token (PAT)** stored as a repository secret, scoped to the specific repositories you need.
- **A `COPILOT_GITHUB_TOKEN` with broader scope** (when your org admin has granted cross-repo access).

In most cases, a fine-grained PAT is the right choice. It is explicit, auditable, and easy to revoke.

### Create a fine-grained PAT for the target repository

1. Go to **Settings → Developer settings → Personal access tokens → Fine-grained tokens** on GitHub.
2. Click **Generate new token**.
3. Under **Repository access**, choose **Only select repositories**, then select the repository you want the agent to read.
4. Under **Permissions**, grant **Contents: Read-only**.
5. Click **Generate token** and copy the value.
6. In your _primary_ repository, go to **Settings → Secrets and variables → Actions** and click **New repository secret**.
7. Name it `CROSS_REPO_TOKEN` and paste the token value.

<details>
<summary>🔐 Why a fine-grained PAT instead of a classic PAT?</summary>

Classic PATs grant access to _all_ repositories in your account. Fine-grained PATs let you specify exactly which repositories are in scope and which permissions apply. This **least-privilege** approach limits the blast radius if a token is ever leaked or an agent is misdirected.

</details>

### Declare the secret and extra permissions in your workflow

Open your `daily-status.md` workflow file (or a copy) and update the frontmatter to declare the new secret and the permissions the agent needs:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 9 * * 1-5"
  workflow_dispatch: {}

permissions:
  contents: write
  issues: write

env:
  CROSS_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
  CROSS_REPO: octocat/hello-world   # ← replace with your target repo

tools:
  - github

safe-outputs:
  - create-issue
---
```

> [!TIP]
> Replace `octocat/hello-world` with the full `owner/repo` name of the repository you want to read. Keeping the repo name in `env:` makes it easy to change later without editing the agent brief.

### Update the agent brief to use the second repository

Below the frontmatter in the same file, update the Markdown brief so the agent knows to query the second repository. Example:

```markdown
You are a daily status reporter. Do the following:

1. Use `CROSS_REPO_TOKEN` to read the last 5 open issues from `${{ env.CROSS_REPO }}`.
2. Summarise those issues in plain language (one sentence each).
3. Combine the summary with the last 5 issues from this repository.
4. Create a new issue titled "Daily Cross-Repo Status — {date}" with both summaries.
```

The `github` tool in gh-aw can accept an alternative token for cross-repo calls. When you reference `CROSS_REPO_TOKEN` in the brief, the agent will use it automatically for requests to the target repository.

### Compile and run

Save the file, then compile it to regenerate the lock file:

```bash
gh aw compile
```

<details>
<summary>🖥️ UI alternative — no local compile needed</summary>

Commit your changes directly in the GitHub editor. On the next scheduled run (or manual dispatch), GitHub Actions will compile and run the workflow. Check the **Actions** tab log to confirm the agent fetched data from both repositories.

</details>

Trigger a manual run:

1. Go to **Actions → daily-status → Run workflow**.
2. Click **Run workflow**.
3. Open the run log and watch for the agent fetching issues from both repositories.
4. Confirm a new issue was created in your primary repository combining both summaries.

### Review the audit trail

After the run, check the audit output:

```bash
gh aw audit --run-id <run-id>
```

Look for tool calls to the target repository — they should show the cross-repo request and the token used. Any failed requests will surface here before they cause silent data gaps.

## ✅ Checkpoint

- [ ] You created a fine-grained PAT scoped to only the target repository with **Contents: Read-only**
- [ ] You stored the PAT as a repository secret named `CROSS_REPO_TOKEN`
- [ ] You declared the secret in `env:` and referenced the target repository in your workflow frontmatter
- [ ] You updated the agent brief to instruct the agent to read from the second repository
- [ ] You compiled the workflow and triggered a manual run successfully
- [ ] You confirmed the run log shows tool calls to both repositories
- [ ] You reviewed the audit output and saw the cross-repo calls listed

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

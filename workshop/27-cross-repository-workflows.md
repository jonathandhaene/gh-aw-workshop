<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Trigger and Coordinate Workflows Across Repositories

> _Production teams rarely work in a single repository — learn how to let one agentic workflow reach across repo boundaries to gather context, create issues, and coordinate work._

## 🎯 What You'll Do

You will update your workflow to read from a second repository and create an issue in a target repository. By the end of this step, you'll understand how cross-repository event triggers work, which permissions they require, and the security considerations that matter most in an enterprise environment.

## 📋 Before You Start

- You have a working agentic workflow from [Test and Improve Your Workflow](12-test-and-iterate.md) or later.
- You have at least two GitHub repositories available — your practice repository and one more (any public repository, or a second repo you own).
- Your workflow uses `GITHUB_TOKEN` permissions that match what's needed for each repository you'll access.

> [!NOTE]
> Cross-repository access requires careful permission scoping. The [cross-repository reference](https://github.github.com/gh-aw/reference/cross-repository/) covers each pattern in detail.

## Steps

### Understand the two patterns

There are two main ways to work across repositories:

1. **Read from another repo** — your agent fetches content (issues, pull requests, files) from a second repository using an authenticated API call or the GitHub MCP server.
2. **Write to another repo** — your agent creates an issue, comment, or PR in a different repository using a fine-grained personal access token (PAT) or GitHub App with access to both repos.

Reading is simpler and works with `GITHUB_TOKEN`. Writing to another repo always requires a cross-repo token.

### Set up cross-repository reading

The `GITHUB_TOKEN` that Actions injects into every run is scoped to the repository that owns the workflow. To read from a second repository, you can use the GitHub MCP server (which makes authenticated GitHub API calls) or the built-in `github-tools` available in the workflow agent.

Add or update the `tools:` block in your workflow frontmatter:

```yaml
---
name: cross-repo-status
on:
  workflow_dispatch:
permissions:
  issues: write
  contents: read
tools:
  - github-tools
---
```

In your task brief, instruct the agent to read from the second repository by name:

```
Fetch the five most recently opened issues from the repository `<owner>/<other-repo>`.
Summarise each issue in one sentence. Then create a new issue in this repository
titled "Cross-repo digest: <date>" with the summaries as the body.
```

Replace `<owner>/<other-repo>` with an actual repository path before running.

### Grant write access to a second repository

If your workflow needs to **create** content in another repository, `GITHUB_TOKEN` is not enough. You need an additional secret containing a token with access to the target repository.

There are two good options:

| Option | When to use |
|--------|-------------|
| Fine-grained PAT (personal) | Personal projects, quick experiments |
| GitHub App installation token | Enterprise teams, CI/CD at scale |

For this exercise, a fine-grained PAT is the quickest path:

1. Go to **github.com** → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens**.
2. Click **Generate new token**.
3. Under **Repository access**, select the target repository.
4. Under **Permissions**, set **Issues: Read and write**.
5. Click **Generate token** and copy the value.
6. In your practice repository, go to **Settings** → **Secrets and variables** → **Actions**.
7. Click **New repository secret**, name it `CROSS_REPO_TOKEN`, and paste the token value.

Now reference the secret in your workflow frontmatter:

```yaml
---
name: cross-repo-writer
on:
  workflow_dispatch:
permissions:
  contents: read
env:
  TARGET_REPO_TOKEN: ${{ secrets.CROSS_REPO_TOKEN }}
tools:
  - github-tools
---
```

> [!NOTE]
> Never commit a raw token to your repository. Always store tokens as encrypted secrets and reference them with `${{ secrets.SECRET_NAME }}`.

### Use `repository_dispatch` to chain workflows across repositories

You can also trigger a workflow in repository B from a workflow run in repository A using the `repository_dispatch` event. Repository B's workflow listens for a custom event type:

```yaml
---
name: downstream-handler
on:
  repository_dispatch:
    types: [status-report-ready]
permissions:
  issues: write
---
```

Repository A's agent brief sends the event:

```
After completing the status summary, send a repository_dispatch event with
event-type "status-report-ready" to the repository `<owner>/<downstream-repo>`.
Include the summary text as the event payload.
```

This pattern is useful for **fan-out automation**: one central workflow gathers data and then triggers specialised handlers in team or project repositories.

### Test the cross-repository read

1. Edit your task brief in the GitHub UI (pencil icon ✏️ on your workflow `.md` file).
2. Replace the `<owner>/<other-repo>` placeholder with the actual repository path.
3. Commit the change.
4. Go to **Actions**, select your workflow, and click **Run workflow**.
5. Watch the run log — the agent should fetch issues from the second repository and create a digest issue in yours.

<details>
<summary>🖥️ Verifying the output issue</summary>

1. Go to **Issues** in your practice repository.
2. Look for an issue titled **Cross-repo digest: <today's date>**.
3. The body should list five issues from the second repository, each summarised in one sentence.

</details>

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes `tools: [github-tools]` and the correct permissions
- [ ] A manual run completed and created a cross-repo digest issue in your practice repository
- [ ] You can explain the difference between reading from another repo with `GITHUB_TOKEN` and writing to one with a PAT
- [ ] You know where to store tokens and why they must never appear in source code
- [ ] You can describe at least one scenario where `repository_dispatch` adds value over a simple read

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

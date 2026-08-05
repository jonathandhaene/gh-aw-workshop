<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Run Agentic Workflows Across Multiple Repositories

> _Enterprise teams manage dozens or hundreds of repositories — an agentic workflow that spans them all is far more powerful than one scoped to a single repo._

## 🎯 What You'll Do

You will extend your daily-status workflow to read data from more than one repository.
By the end, a single run will summarize activity across several repos and post a consolidated report — a pattern for org-wide health checks and cross-team standup reports.

## 📋 Before You Start

- Your agentic workflow runs successfully on a schedule (see [Test and Iterate](12-test-and-iterate.md)).
- You understand how to pass data into a workflow brief (see [Connect a Live Data Source](16-connect-data-source.md)).
- You know the names of the additional repositories you want to monitor (they must be in the same organization or accessible to the workflow's token).

> [!NOTE]
> The `GITHUB_TOKEN` provided to an agentic workflow is scoped to the repository that owns it. To read other repos in the same org, you need either a fine-grained personal access token with `contents: read` on the target repos, or the GitHub MCP server — which the agent can call using your Copilot session's ambient authorization.

## Understand the cross-repository pattern

An agentic workflow can reach other repositories in two main ways:

| Approach | When to use |
|----------|-------------|
| **GitHub MCP server** | Reading issues, PRs, and code across org repos without extra secrets |
| **Deterministic `gh` step + `$GITHUB_OUTPUT`** | When you need structured, filtered data from specific repos before the agent starts |

For most org-wide reporting use cases, the GitHub MCP server is the right tool — it lets the agent query multiple repos in a single pass without the workflow author having to pre-fetch each one.

## Add multi-repo scope to your brief

Open your workflow file (for example, `.github/workflows/daily-status.md`).

### List the target repositories in the brief

Add a repositories section to your task brief. Paste the list directly in the Markdown body so the agent knows exactly which repos to cover:

```markdown
## Repositories to monitor

- `YOUR_ORG/repo-alpha`
- `YOUR_ORG/repo-beta`
- `YOUR_ORG/repo-gamma`

For each repository, summarise: open issues labelled `priority`, PRs awaiting review, and any CI failures from the last 24 hours.
Then write a combined executive summary at the top.
```

Replace `YOUR_ORG/repo-alpha` etc. with real repository names.

### Verify your MCP tools block

Cross-repository reads require the GitHub MCP server. Confirm or add it in your frontmatter:

```yaml
tools:
  github-mcp:
    scope: read
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the repositories list and the `tools: github-mcp:` block as shown above.
4. Click **Commit changes**.

</details>

### Compile and push

After editing the `.md` file, regenerate the lock file:

```bash
gh aw compile
```

Then commit and push both the `.md` and the updated `.lock.yml`.

## Trigger a test run

1. Go to the **Actions** tab in your repository.
2. Select your daily-status workflow.
3. Click **Run workflow** → **Run workflow**.
4. Open the run log and watch the agent call the MCP server for each repository in your list.
5. Review the final output — it should include per-repo sections and a combined summary.

> [!TIP]
> Start with two or three repositories. Adding too many at once can push the run toward its token limit. Increase the list gradually and monitor AIC usage with `gh aw forecast`.

## ✅ Checkpoint

- [ ] Your task brief names at least two target repositories
- [ ] Your frontmatter includes `tools: github-mcp: scope: read`
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] A manual run completed and the run log shows MCP calls for each repository
- [ ] The workflow output includes a separate section for each repository and a combined summary
- [ ] You can explain why the `GITHUB_TOKEN` alone is not enough for cross-repository reads

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

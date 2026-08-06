<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Read Data from Other Repositories

> _One agentic workflow can reach across repository boundaries — giving you a fleet-wide view without duplicating configuration._

## 🎯 What You'll Do

You'll update your daily-status workflow to read data from a second repository (or a list of repositories), then confirm the agent synthesises that cross-repository context into its output. By the end, you'll understand when cross-repository access is the right tool and what permissions it requires.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) or [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your daily-status workflow runs successfully and posts output via a safe output.
- _(Enterprise users)_ Confirm that your GitHub admin has granted the `GITHUB_TOKEN` or fine-grained PAT access to the additional repositories you want to read.

## Steps

### Understand the access model

By default, a workflow's `GITHUB_TOKEN` is scoped to **the repository it runs in**. To read another repository, you have two options:

- **GitHub MCP proxy** — when your frontmatter includes a `tools: github: mode: gh-proxy` block, the agent's GitHub tool calls flow through a proxy that uses a Copilot-managed token. That token can read other repositories in the same organisation that the Copilot app has access to.
- **Fine-grained PAT** — create a token scoped to specific target repositories and expose it as a repository secret.

For most organisation use cases, the MCP proxy path is the easiest because there is no separate token to manage.

> [!NOTE]
> Cross-repository **writes** require explicit `contents: write` permission on each target repository. This workshop step covers **reads only**. Keep your footprint small: read-only access minimises the blast radius if an adversarial instruction ever redirects your agent.

### Add a target repository to your workflow brief

Open `.github/workflows/daily-status.md` and add a line to your task brief that tells the agent which repository to pull extra context from. For example:

```markdown
Also summarise recent activity in **org-name/platform-team**, focusing on
any open issues labelled `incident` opened in the last 48 hours.
```

Replace `org-name/platform-team` with a real repository you have read access to. If you are working in a personal account, use any public repository — for example `actions/toolkit`.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to your repository on GitHub.
2. Open `.github/workflows/daily-status.md` and click the **pencil icon (✏️)**.
3. Add the cross-repository instruction to the agent brief section.
4. Click **Commit changes**.

</details>

### Verify your `tools:` block

Cross-repository reads via the MCP proxy require the `github` tool to be enabled. Confirm your frontmatter looks like this:

```yaml
---
name: Daily Status
on:
  schedule: daily on weekdays
  workflow_dispatch: {}
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  github:
    mode: gh-proxy
safe-outputs:
  - create-issue-comment
---
```

If `tools:` is missing, add it and recompile.

### Recompile and push

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "feat: add cross-repository context to daily status"
git push
```

<details>
<summary>🖥️ GitHub UI alternative for commit</summary>

If you made edits in the GitHub editor, your changes are already committed. Navigate to the **Actions** tab to trigger a manual run.

</details>

### Run and verify

1. Go to **Actions** in your repository and select the **Daily Status** workflow.
2. Click **Run workflow** to trigger a manual run.
3. When the run completes, open the log and look for tool calls that reference the target repository name. You should see the agent fetching issues or pull requests from that repo.
4. Check the output — the summary should include activity from both your primary repository and the target repository.

> [!TIP]
> If the log shows `403` errors when the agent calls a tool against the target repository, the Copilot app likely does not have access to that repo. Ask your GitHub admin to grant it, or switch to a fine-grained PAT stored as a secret and passed via `env:`.

### When to use cross-repository workflows

Cross-repository access is most useful when you need a **unified view across a team or organisation**, for example:

| Use case | What to aggregate |
|---|---|
| Engineering health report | Open incidents across all service repos |
| Release readiness check | Merged PRs and open blockers across release repos |
| Dependency audit | Security advisories across a monorepo plus its forks |

Keeping the workflow in **one** authoritative repository (rather than deploying it to every repo) reduces maintenance overhead and makes audit trails easier to follow — especially in enterprise environments.

## ✅ Checkpoint

- [ ] Your workflow brief names at least one external repository and what data to fetch from it
- [ ] Your frontmatter includes a `tools: github: mode: gh-proxy` block (or a PAT secret in `env:`)
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] A manual run completed and the Actions log shows tool calls against the external repository
- [ ] The workflow output includes a section summarising activity from the external repository
- [ ] You can explain when cross-repository reads require a fine-grained PAT instead of the MCP proxy

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

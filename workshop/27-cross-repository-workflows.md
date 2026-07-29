<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Run Agentic Workflows Across Repositories

> _When your AI workflow needs to read from or write to multiple repositories at once, [cross-repository access](https://github.github.com/gh-aw/reference/cross-repository/) unlocks a new class of use cases — team-wide reports, multi-repo audits, and org-level intelligence._

## 🎯 What You'll Do

You will extend your daily-status workflow to collect data from a second repository, and configure the permissions it needs to do so safely. By the end of this step, your workflow will summarise activity from at least two repositories in a single run.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have access to at least one other repository on the same GitHub organisation or account.
- _(Enterprise users)_ Your GitHub administrator has confirmed that [cross-repository token access](https://github.github.com/gh-aw/reference/cross-repository/) is permitted by your organisation's Copilot policy.

## Steps

### Understand how cross-repository access works

By default, an agentic workflow can only act on the repository it runs in. The `GITHUB_TOKEN` GitHub Actions provides is scoped to that single repository.

To reach a second repository, you have two options:

| Approach | Best for |
|----------|----------|
| **`repos:` list in frontmatter** | Read-only access to one or more additional repos in the same org |
| **Fine-grained PAT stored as a secret** | Read-write access or access across organisations |

For most multi-repo reporting use cases, the `repos:` list is the right choice. It is simpler, auditable, and requires no long-lived credentials.

> [!NOTE]
> Cross-repository writes (opening issues, pushing branches) in another repository require a fine-grained PAT with the appropriate permissions. Read [Side Quest: Long-Lived Credential Risks](side-quest-16-05-long-lived-credentials.md) before using PATs in workflows.

### Add the `repos:` list to your frontmatter

Open `.github/workflows/daily-status.md` and add a `repos:` block inside your existing frontmatter:

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the file editor.
3. Add the `repos:` block shown below to the frontmatter section.
4. Click **Commit changes**.

</details>

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
repos:
  - owner/other-repo
---
```

Replace `owner/other-repo` with the real `org/repo` name you want to include.

The `repos:` key tells gh-aw to request an additional scoped token for each listed repository. Your task brief can then reference those repositories by name and the agent will be able to read their data.

> [!IMPORTANT]
> Every repository you add to `repos:` must be in the same GitHub organisation (or your account) as the workflow repository. Cross-organisation access requires a PAT.

### Update your task brief to reference the second repository

Below the frontmatter, update your task brief to mention the additional repository. Be explicit about which repository each data point should come from:

```markdown
## Daily Status Report

You are generating a daily summary for the team.

1. Fetch the last 24 hours of merged PRs and closed issues from **{{ env.GITHUB_REPOSITORY }}**.
2. Fetch the last 24 hours of merged PRs and closed issues from **owner/other-repo**.
3. Combine both lists into a single Markdown summary, grouped by repository.
4. Post the summary as a comment on the latest open issue in {{ env.GITHUB_REPOSITORY }} tagged `daily-report`.
```

Using `{{ env.GITHUB_REPOSITORY }}` for the primary repository means the brief stays correct if you fork or rename the repo.

### Compile and test the updated workflow

Compile locally to catch any frontmatter errors before pushing:

```bash
gh aw compile
```

Push the updated `.md` and `.lock.yml` files, then trigger a manual run:

```bash
gh workflow run daily-status.lock.yml
```

Open the run log and confirm the agent made tool calls referencing the second repository.

> [!TIP]
> If the run log shows a `403` or "resource not accessible" error for the second repository, check that the repository name in `repos:` exactly matches the `owner/repo` slug, and confirm that the organisation's Copilot policy allows cross-repository access.

### Enterprise: verify policy and audit trail

Enterprise teams should take two additional steps:

1. **Confirm the policy**: ask your GitHub admin to verify that `Allow additional repository access` is enabled in **Organization settings → Copilot → Policies**.
2. **Review the audit log**: cross-repository token grants appear in the organisation's audit log under `copilot.cross_repo_token_granted`. After your run, confirm the event is present.

These two steps are part of a responsible enterprise rollout — they ensure that cross-repository access is intentional and traceable.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `repos:` block listing at least one additional repository
- [ ] Your task brief references both repositories by name
- [ ] `gh aw compile` completed without errors and the updated `.lock.yml` is committed and pushed
- [ ] A manual run completed successfully and the log shows tool calls to the second repository
- [ ] The workflow output includes data from both repositories
- [ ] _(Enterprise users)_ You confirmed the organisation policy allows cross-repository access
- [ ] _(Enterprise users)_ You located the `copilot.cross_repo_token_granted` event in the organisation audit log

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

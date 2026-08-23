<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Cross-Repository Agentic Workflows

> _Most real engineering work spans more than one repository — your workflows can too._

## 🎯 What You'll Do

You'll extend your agentic workflow to read data from multiple repositories in a single run. By the end of this step, your workflow will produce an org-wide summary that pulls from several repos rather than just the one it lives in — a common pattern for platform teams, security reviews, and engineering leadership reporting.

## 📋 Before You Start

- You have a working scheduled agentic workflow from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and are comfortable reading run logs.
- Your workflow targets an organisation where you have `read` access to the repositories you want to query. On GHES or GHEC, confirm your personal access token or app installation covers those repositories.

## Steps

### Understand the cross-repository model

By default, an agentic workflow has access only to the repository it runs in. The [cross-repository](https://github.github.com/gh-aw/reference/cross-repository/) feature lets you list additional repositories that the agent may read from during the run.

Two access patterns exist:

| Pattern | When to use |
|---|---|
| **Explicit repo list** | You know the exact repos at author time (e.g., a fixed set of services). |
| **Dynamic discovery** | You need to scan all repos matching a filter at run time (e.g., all repos with the `platform-team` topic). |

This step covers the explicit list pattern. Dynamic discovery builds on the same frontmatter and is a natural extension once you have the basics working.

### Add `cross-repository` to your workflow frontmatter

Open your daily-status workflow file — for example `.github/workflows/daily-report-status.md` — and add a `cross-repository:` block inside your frontmatter:

```yaml
---
name: org-wide daily status
on:
  schedule:
    - cron: '0 8 * * 1-5'
  workflow_dispatch:

permissions:
  contents: read
  issues: read
  pull-requests: read

cross-repository:
  repos:
    - owner/repo-a
    - owner/repo-b
    - owner/repo-c
---
```

Replace `owner/repo-a`, `owner/repo-b`, and `owner/repo-c` with the real repository slugs you want to include.

> [!NOTE]
> On GHES or GHEC with SSO-enforced repositories, the identity that runs the workflow must have an active SSO session or a token authorised for those repositories. If you see `403` errors in the run log, check token scope and SSO authorisation first.

### Update the task brief

Your task brief needs to tell the agent which repositories to read from and what to look for. Replace the task brief in your workflow file with something like:

```markdown
You have read access to the following repositories: owner/repo-a, owner/repo-b, owner/repo-c.

For each repository:
1. List any pull requests opened or merged in the last 24 hours.
2. List any issues opened or closed in the last 24 hours.
3. Flag any repository that has no activity — that is useful signal too.

Produce a single Markdown summary organised by repository. Keep each section concise: three to five bullet points maximum. End with a one-paragraph overall health assessment.

Use safe-outputs to post the summary as a new issue titled "Daily Org Status — {date}".
```

Adjust the instructions to match your team's needs. The key principle is that the brief describes intent, and the agent uses the cross-repository access to fulfil it.

### Compile and push

Run `gh aw compile` to regenerate the lock file:

```bash
gh aw compile
```

Then commit and push both the `.md` file and the updated `.lock.yml`:

```bash
git add .github/workflows/daily-report-status.md \
        .github/workflows/daily-report-status.lock.yml
git commit -m "feat: add cross-repository access for org-wide summary"
git push
```

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. In your repository on GitHub, navigate to `.github/workflows/daily-report-status.md`.
2. Click the **pencil icon (✏️)** to edit the file.
3. Add the `cross-repository:` block to the frontmatter.
4. Update the task brief section.
5. Click **Commit changes**.
6. The `gh aw compile` step requires a terminal. If you are UI-only, trigger a manual run — GitHub Actions will compile on the runner and report any errors in the run log.

</details>

### Trigger a manual run and read the output

Go to your repository's **Actions** tab and trigger a manual run of the workflow. Once the run completes, open the run log and confirm:

- The agent read from each repository listed in `cross-repository.repos`.
- The output issue contains sections for each repository.
- Any repository with no activity is noted, not silently skipped.

![Cross-repository run log showing per-repo activity sections](images/27-cross-repo-run-log.png)

> [!TIP]
> If a repository returns no data, that may mean the token does not have access — not that the repository is empty. Check the `firewall.md` artifact in the run's artifacts zip for blocked network calls.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `cross-repository:` block listing at least two repositories
- [ ] The task brief names those repositories and describes what to collect from each
- [ ] `gh aw compile` completed without errors and the `.lock.yml` was updated
- [ ] A manual run completed and the Actions log shows the agent reading from each listed repository
- [ ] The output issue (or log) contains a section for every repository, including any with no activity
- [ ] You can explain the difference between the explicit repo list and dynamic discovery patterns
- [ ] On GHES or GHEC: you verified that token scope and SSO authorisation cover the listed repositories

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

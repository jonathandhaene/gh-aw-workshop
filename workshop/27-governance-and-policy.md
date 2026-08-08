<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Agentic workflows have real-world impact — so teams and enterprises need clear policies around who can create them, what they can do, and how their actions are reviewed._

## 🎯 What You'll Do

Apply a practical governance model to your agentic workflows. By the end, you will have restricted what your workflow can write, scoped its permissions to only what it needs, and created a pattern you can apply across every workflow you ship.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your `daily-status` workflow is compiled and running in GitHub Actions.
- You can edit workflow files in the GitHub UI or in your Codespace.

## Steps

### Understand why governance matters for agentic workflows

A traditional GitHub Actions job does what its YAML instructs — deterministically. An agentic workflow runs reasoning steps: it reads context, plans actions, and calls tools. That makes governance especially important because:

- The agent can call multiple tools in a single run.
- Its outputs (issues, comments, PRs) are visible to your whole organisation.
- A poorly scoped workflow can accumulate permissions it does not need.

Good governance answers three questions: **who can create workflows**, **what can a workflow do**, and **who reviews what it did**.

### Review and tighten your workflow permissions

Open `.github/workflows/daily-status.md` in the editor (pencil icon ✏️ on GitHub, or in your Codespace). Look at the `permissions:` block in the frontmatter.

A well-scoped daily-status workflow needs only read access to repository content and write access to one output channel. If yours grants broader permissions, restrict it now:

```yaml
permissions:
  contents: read
  issues: write
```

If your workflow posts to a discussion instead of an issue, replace `issues: write` with `discussions: write`. Remove any permission you don't use.

<details>
<summary>🖥️ GitHub UI path</summary>

1. Open your repository on GitHub and navigate to `.github/workflows/daily-status.md`.
2. Click the **pencil icon (✏️)** to edit the file.
3. Update the `permissions:` block as shown above.
4. Scroll to the bottom and click **Commit changes**.

</details>

### Use branch protection to control who can merge workflow changes

Agentic workflows live in `.github/workflows/`. Anyone who can push to that directory can change what your agent does. Protect it with branch protection rules:

1. Go to your repository on GitHub and click **Settings** → **Branches**.
2. Under **Branch protection rules**, click **Add rule**.
3. Set **Branch name pattern** to `main` (or your default branch).
4. Enable **Require a pull request before merging** and set at least **1 required reviewer**.
5. Click **Save changes**.

Now every change to a workflow file requires a peer review before it can run in production.

> [!TIP]
> Enterprise teams on GitHub Enterprise Cloud (GHEC) can enforce these rules at the organisation or repository ruleset level, so they apply automatically to every repository in the organisation. See [GitHub repository rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) for details.

### Document your workflow's intended behaviour

Add a brief policy comment to the top of your workflow's Markdown body, just below the frontmatter closing fence. Describe what the workflow is allowed to do and what it should never do:

```yaml
<!--
Policy: This workflow reads open issues created in the last 24 hours and posts
a daily summary comment on the repository's pinned tracking issue. It must not
close issues, create new issues, or modify repository files.
-->
```

This comment travels with the workflow file through code review, is visible in audit logs, and gives your team a quick reference when reviewing run output.

### Verify the minimal-permission run

After committing your changes:

1. Open the **Actions** tab and trigger a manual run of your daily-status workflow.
2. Click the completed run and expand the **Summary** section.
3. Confirm the run succeeded and that no permission error appeared in the log.
4. If you see a `403` or `Resource not accessible by integration` error, add back the missing permission and re-run.

> [!NOTE]
> GitHub Actions shows the resolved effective permissions for each run in the job log under **Set up job** → **GITHUB_TOKEN permissions**. Use this to verify that your `permissions:` block was applied correctly.

## ✅ Checkpoint

- [ ] Your workflow's `permissions:` block lists only the permissions actually used (no extras)
- [ ] You committed the updated `permissions:` block and `gh aw compile` passed without errors
- [ ] Both `daily-status.md` and `daily-status.lock.yml` are committed and pushed
- [ ] Branch protection is enabled on your default branch with at least one required reviewer
- [ ] You added a policy comment to the workflow Markdown body describing what the workflow may and may not do
- [ ] A manual run completed successfully with the tightened permissions
- [ ] You can locate the resolved `GITHUB_TOKEN` permissions in the **Set up job** log for the run
- [ ] You can explain the three governance questions every agentic workflow should answer

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

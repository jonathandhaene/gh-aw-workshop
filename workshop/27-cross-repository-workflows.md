# Trigger Workflows Across Repositories

> _Most real-world teams don't live in a single repository — learn how to coordinate agentic workflows across the repos that already hold your data._

## 🎯 What You'll Do

You'll update your agentic workflow so it can read from, or dispatch to, a second repository in your organisation. By the end, you'll understand when cross-repository wiring makes sense and how to do it safely.

## 📋 Before You Start

- Completed [Run and Watch Your Workflow](08-run-your-workflow.md) — you have at least one working workflow.
- You own (or have write access to) a second repository to target, or you can create a new one at [github.com/new](https://github.com/new).

## Why Cross-Repository Workflows?

An agentic workflow runs inside one repository, but the information it needs often lives elsewhere. Examples:

- A **status report** workflow in `.github-automation` that reads issues from `product-roadmap`.
- A **code-review helper** in `platform` that monitors PRs in a dozen service repositories.
- An **enterprise release coordinator** that aggregates data from every team repo under an organisation.

The `cross-repository` feature lets a single workflow definition serve an entire org without duplicating it.

> [!NOTE]
> Cross-repository reads are subject to the permissions you grant in the workflow's `permissions:` block. A workflow can only read from or write to repositories where its token has been explicitly granted access. Review the [permissions reference](https://github.github.com/gh-aw/reference/permissions/) before enabling broad access.

## Steps

### Grant cross-repository permissions

Open your workflow's `.md` file and add (or extend) the `permissions:` block in the YAML frontmatter:

```yaml
---
name: Cross-Repo Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: read
  pull-requests: read
---
```

The `contents: read` permission lets the agent fetch file contents from other repositories in the same organisation when the workflow token is scoped to the org. For tighter control, list specific repositories under an `org-repositories` key — see the [cross-repository reference](https://github.github.com/gh-aw/reference/cross-repository/) for syntax details.

### Reference the target repository in your brief

In the Markdown body (your agent brief), tell the agent which repository to inspect:

```markdown
## Task

Check the open pull requests in `my-org/product-roadmap`.
Summarise any PRs that have been open for more than 7 days and have no reviewer assigned.
Post a brief Markdown summary as a comment on issue #1 in **this** repository.
```

Replace `my-org/product-roadmap` with your actual org and repository name.

### Commit and trigger the workflow

1. Save the file. Use the pencil icon (✏️) on GitHub to edit inline, then click **Commit changes**.
2. Navigate to **Actions** → select your workflow → click **Run workflow**.

<details>
<summary>🖥️ Terminal alternative</summary>

```bash
git add .github/workflows/cross-repo-status.md
git commit -m "Add cross-repository data source"
git push
gh workflow run cross-repo-status.md
```

</details>

### Read the run output

When the run finishes, open the **Actions** run log and expand the agent step. Look for the tool calls the agent made — you should see it fetching PR data from the target repository before generating its summary.

![Cross-repository run log showing tool calls to the target repo](images/27-cross-repo-run-log.png)

If the agent reports a permissions error, return to your frontmatter and verify the `permissions:` block. Enterprise installations may also require an admin to approve cross-repository token scopes — check with your GitHub Enterprise administrator.

> [!TIP]
> Start with a single source repository and a read-only query. Expand scope only after you've confirmed the output is accurate and the permissions are as narrow as possible.

### (Optional) Dispatch to a target repository

Your workflow can also _trigger_ another workflow in a different repository using a `workflow_dispatch` or `repository_dispatch` event from a deterministic step:

```yaml
- name: Notify downstream repo
  uses: actions/github-script@v7
  with:
    script: |
      await github.rest.repos.createDispatchEvent({
        owner: 'my-org',
        repo: 'ops-alerts',
        event_type: 'new-status-report',
        client_payload: { source: context.repo.repo }
      });
```

This keeps agentic reasoning in the source workflow and delegates follow-up actions to a separate, targeted workflow.

## ✅ Checkpoint

- [ ] You added a `permissions:` block to your workflow frontmatter scoped to the minimum required access
- [ ] Your agent brief references a specific external repository by `org/repo` name
- [ ] The workflow ran successfully and the agent's output reflects data from the target repository
- [ ] You can explain why cross-repository access requires explicit permission grants
- [ ] You identified at least one real use case from your own work that could benefit from cross-repository coordination

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

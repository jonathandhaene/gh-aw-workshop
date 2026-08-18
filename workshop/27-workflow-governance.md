# Govern Your Agentic Workflows with Required Approvals

> _Production agentic workflows need the same human gates you apply to any automated system that can write to your repositories._

## 🎯 What You'll Do

You'll add a required reviewer to an environment so that an agentic workflow pauses before it writes to your repository. By the end, you'll have a governance layer that lets a human approve or reject each run before its output lands.

## 📋 Before You Start

- You've completed [Run Your Agentic Workflow](08-run-your-workflow.md) and have a workflow running in GitHub Actions.
- You have **Admin** access to the repository (you need it to create environments).

## Steps

### Create a protected environment

Environments let you attach protection rules — including required reviewers — to any job that targets them.

1. In your repository on GitHub, click **Settings** → **Environments** (left sidebar).
2. Click **New environment**.
3. Name it `production` and click **Configure environment**.
4. Under **Protection rules**, tick **Required reviewers**.
5. Type your own GitHub username in the reviewer field and click **Save protection rules**.

![Environments settings page with Required reviewers enabled](images/27-environment-reviewers.png)

### Point your workflow at the environment

Open your workflow file (for example, `daily-report-status.md`) and add an `environment:` key to the frontmatter.

```yaml
---
name: Daily Report Status
on:
  schedule: daily
environment: production
permissions:
  contents: write
  issues: write
---
```

> [!NOTE]
> The `environment:` key targets the job at the `production` environment. GitHub Actions will pause the run and send a review request to everyone listed as a required reviewer before the agent starts.

<details>
<summary>🖥️ Editing the workflow file in the GitHub UI</summary>

1. In your repository, navigate to `.github/workflows/daily-report-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `environment: production` below the `on:` line in the frontmatter.
4. Click **Commit changes**.

</details>

If you prefer to edit locally, run `gh aw compile` after saving to regenerate the lock file, then commit both files.

### Trigger a run and approve it

1. Go to **Actions** in your repository and click your workflow.
2. Click **Run workflow** → **Run workflow** to trigger a manual run.
3. The run will pause at the job step and show a yellow **Waiting** badge.
4. Click into the run. You'll see a banner: _"This workflow requires approval."_
5. Click **Review deployments**, tick the `production` checkbox, add an optional comment, and click **Approve and deploy**.

The workflow resumes and completes normally. The approval is recorded in the run log.

> [!TIP]
> You can also **reject** a run here. The job is cancelled and no agent actions are taken — useful when you spot an unexpected trigger or want to pause automation during an incident.

### Review the approval record

Every approval is stored as part of the Actions run.

1. Open the completed run.
2. Scroll to the **deployment** entry at the top of the timeline.
3. Click **View deployment** to see who approved, when, and any comment they left.


## ✅ Checkpoint

- [ ] You created a `production` environment with at least one required reviewer
- [ ] You added `environment: production` to your workflow frontmatter
- [ ] You triggered a manual run and saw the **Waiting** pause before the agent started
- [ ] You approved the run from the GitHub Actions UI
- [ ] You located the approval record in the completed run timeline
- [ ] You understand how to reject a run and what effect that has on the agent

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

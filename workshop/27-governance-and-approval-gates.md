<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Governance and Approval Gates for Agentic Workflows

> _Agentic workflows that write to production need a human checkpoint — environment protection rules let you require a named reviewer to approve every run before it touches critical resources._

## 🎯 What You'll Do

You will add a GitHub environment with a required reviewer to your agentic workflow. When the workflow runs, GitHub Actions will pause at the protected environment step and wait for an explicit approval before the agent is allowed to write any output.

## 📋 Before You Start

- You have a working agentic workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or any earlier step.
- Your repository is hosted on GitHub (github.com, GitHub Enterprise Cloud, or GitHub Enterprise Server 3.9+).
- You have **Admin** access to the repository so you can manage environments.

> [!NOTE]
> <details>
> <summary><b>Enterprise users: check your organisation policy before proceeding.</b></summary>
>
> On GitHub Enterprise Server and GitHub Enterprise Cloud, organisation administrators can restrict who can create or modify environments. If you receive a "permission denied" error when creating an environment, ask your organisation admin to either grant you the necessary role or create the environment on your behalf.
>
> </details>

## Steps

### Create a protected environment

1. Open your repository on GitHub.
2. Click **Settings** → **Environments** → **New environment**.
3. Name the environment `production` and click **Configure environment**.
4. Under **Deployment protection rules**, tick **Required reviewers**.
5. Type your own GitHub username (or a teammate's) in the reviewer box and click **Save protection rules**.

![Environment settings showing Required reviewers enabled](images/27-environment-required-reviewer.svg)

### Point your workflow at the environment

Open your agentic workflow `.md` file — for example `.github/workflows/daily-status.md` — and add an `environment:` field to the frontmatter:

```yaml
---
name: daily-status
on:
  schedule: daily on weekdays
environment: production
permissions:
  contents: write
  issues: write
---
```

The `environment:` key tells GitHub Actions to evaluate the environment's protection rules before the job starts.

<details>
<summary>🖥️ Edit in the GitHub UI</summary>

1. In your repository, navigate to `.github/workflows/daily-status.md`.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add `environment: production` on a new line inside the `---` frontmatter block, directly below the `name:` field.
4. Click **Commit changes**, write a short commit message, and click **Commit changes** again.

</details>

If you have `gh aw` installed in a terminal, regenerate the compiled lock file:

```bash
gh aw compile
```

Then commit and push both the `.md` file and the updated `.lock.yml`.

> [!TIP]
> If you rely on the GitHub Actions UI path (no local `gh aw`), you can skip the compile step. GitHub Actions will detect that the lock file is out of date and recompile it automatically on the next push.

### Trigger the workflow and approve the run

1. In your repository, click **Actions** and select your workflow.
2. Click **Run workflow** → **Run workflow** to start a manual run.
3. The job will pause at a yellow **Waiting** state. You will see a banner: _"This workflow is waiting for approval from a required reviewer."_
4. Click **Review deployments**, tick the **production** checkbox, and click **Approve and deploy**.
5. The agent resumes and completes the run.

![Actions UI showing the Approve and deploy button](images/27-approve-and-deploy.svg)

> [!NOTE]
> Each reviewer receives an email and an in-app notification when a run is waiting. On GitHub Enterprise Cloud, you can also route notifications to Slack via the GitHub app.

### Audit the approval record

After the workflow completes, click the completed job and scroll to the **Review history** section. GitHub records the reviewer's username and the exact timestamp of the approval. This creates an immutable audit trail — useful for compliance requirements such as SOC 2 or change-management policies.

## ✅ Checkpoint

- [ ] You created a `production` environment with at least one required reviewer in **Settings → Environments**
- [ ] You added `environment: production` to your workflow's frontmatter
- [ ] A manual workflow run paused at the **Waiting** state before your agent ran
- [ ] You approved the deployment and the workflow completed successfully
- [ ] You can find the approval timestamp in the job's **Review history** section
- [ ] You can explain why an environment gate is more reliable than a manual process for enforcing human-in-the-loop approvals

<!-- journey: all -->
Want to revisit another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

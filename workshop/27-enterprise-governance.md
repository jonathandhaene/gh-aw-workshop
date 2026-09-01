<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Apply Enterprise Governance to Your Agentic Workflows

> _Agentic workflows that write to repositories, post comments, or open pull requests can have broad impact — enterprise governance gives your organisation a safety net without slowing teams down._

## 🎯 What You'll Do

You'll configure three enterprise-ready guardrails for your agentic workflow: required reviewers for workflow deployments, a scoped `permissions:` block that follows the principle of least privilege, and a retention-aware audit policy. By the end you'll have a workflow that is ready for an internal enterprise review.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your `daily-status.md` workflow is committed and has run at least once.
- _(GHES users)_ You have completed [Enterprise Setup Considerations](side-quest-enterprise-setup.md).
- You can reach a repository or organisation administrator to enable the required settings, or you are the owner of the repository you are using for this workshop.

## Steps

### Understand why governance matters for agentic workflows

A classic GitHub Actions workflow is deterministic: every output is known at author time. An agentic workflow is different — the AI model chooses what to write, which tool to call, and how to phrase output. That flexibility is powerful, but it raises three governance concerns:

1. **Unintended writes** — a misconfigured workflow could open issues, post comments, or commit files without human sign-off.
2. **Overprivileged tokens** — a workflow with `contents: write` can overwrite any file if the model is misdirected.
3. **Audit gaps** — enterprise compliance teams need to know who changed a workflow, when, and why.

The guardrails in this step address all three.

### Lock down permissions with least-privilege scoping

Open `daily-status.md` (or the workflow you built in earlier steps). Locate the `permissions:` block in the frontmatter and update it to grant **only** what the workflow actually needs.

A daily-status workflow that reads issues and posts a comment needs:

```yaml
permissions:
  issues: write
  contents: read
  pull-requests: read
```

Remove any permission that is not used — for example, `contents: write` is unnecessary if your workflow never commits files.

> [!TIP]
> Run `gh aw compile` after editing frontmatter to catch YAML errors before you push. UI-first learners can commit directly and let GitHub Actions surface compile errors in the run log.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Edit the `permissions:` block in the frontmatter.
4. Click **Commit changes**, add a brief message like `chore: tighten permissions`, and commit directly to the default branch.

</details>

### Require pull-request review for workflow changes

Enterprise teams commonly require that changes to workflow files go through a pull request review before they can be deployed. Configure this in your repository settings.

1. In your repository on GitHub, click **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit an existing rule for your default branch).
3. Under **Require a pull request before merging**, enable the rule and set **Required approvals** to at least **1**.
4. Under **Restrict pushes that create matching branches**, add `.github/workflows/` as a protected path if your GitHub plan supports path-level rules.

> [!NOTE]
> Path-level branch protection rules are available on **GitHub Enterprise Cloud** and **GitHub Enterprise Server 3.14+**. On other plans, the standard branch protection rule still requires a PR review for any push to the protected branch.

From this point on, any change to a workflow file goes through a PR and requires at least one approval. This creates a natural audit trail in the pull request history.

### Add a workflow-level audit comment

Agentic workflows can self-document each run by including a brief audit summary in their safe output. Open your workflow's task brief and add a sentence instructing the agent to include the run ID and date in every output.

Example instruction to add to your task brief:

```
Always start your output with a single line:
> **Run:** #${{ github.run_number }} — ${{ github.run_id }} — ${{ github.event_name }} — ${{ github.actor }}
```

This embeds the GitHub run number, event type, and triggering actor into every issue comment your workflow posts — making it easy to trace a specific output back to its run log.

### Verify your governance posture

Run your workflow manually one more time (**Actions** → select your workflow → **Run workflow**) and check:

- The run log shows only the permissions you explicitly granted.
- The issue comment includes the audit line with a valid run number and actor.
- If you enabled branch protection, try committing directly to the default branch and confirm that GitHub blocks the push.

## ✅ Checkpoint

- [ ] Your workflow frontmatter has a `permissions:` block listing only the permissions the workflow uses
- [ ] You removed any unused permissions (e.g. `contents: write`) from the block
- [ ] Branch protection or a ruleset is configured to require at least one pull request review before merging workflow changes
- [ ] Your task brief instructs the agent to include the run ID and triggering actor in its output
- [ ] A manual run completed and the issue comment contains the audit line with a real run number
- [ ] You can explain the difference between `contents: read` and `contents: write` and which your workflow needs
- [ ] You know which governance rule applies to path-level protection on your GitHub plan

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

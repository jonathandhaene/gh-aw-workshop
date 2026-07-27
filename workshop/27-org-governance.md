# Govern Agentic Workflows Across Your Organisation

> _Agentic workflows that run unattended need org-level guardrails — not just per-workflow settings._

## 🎯 What You'll Do

You will apply organisation-level controls that limit how agentic workflows can run across every repository in your org. By the end, you will have a mandatory approval environment, a restricted permissions policy, and an org-wide secret strategy that keeps AI-powered automation safe at scale.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have **Owner** access to a GitHub organisation (or a test org you control).
- You understand `permissions:` in workflow frontmatter — see [Step 7](07-your-first-workflow.md) if you need a refresher.

## Steps

### Review what org-level settings control

Three organisation settings work together to contain agentic workflows:

| Setting | Where to find it | What it does |
|---------|-----------------|-------------|
| **Actions permissions** | Org → **Settings → Actions → General** | Limits which actions and workflows can run |
| **Workflow permissions** | Org → **Settings → Actions → General → Workflow permissions** | Sets the default `GITHUB_TOKEN` scope for all repos |
| **Required reviewers (Environments)** | Repo → **Settings → Environments** | Blocks a workflow job until a human approves it |

Start with the workflow permissions baseline — it is the broadest control.

### Tighten the default token scope

Open your organisation's **Actions → General** settings page:

1. Scroll to **Workflow permissions**.
2. Select **Read repository contents and packages permissions** (read-only by default).
3. Click **Save**.

This forces every workflow to declare explicit `permissions:` in its frontmatter whenever it needs to write. Agentic workflows that omit a `permissions:` block can still read, but cannot push commits or open pull requests.

> [!TIP]
> Org-wide read-only is the right starting point. Individual workflows opt in to elevated permissions by declaring them explicitly — the principle of least privilege applied at scale.

### Create a required-review environment

For workflows that write to production repositories, require a human to approve each run before it executes:

1. Go to a repository → **Settings → Environments** → **New environment**.
2. Name it `agentic-production`.
3. Enable **Required reviewers** and add one or more trusted team members.
4. Click **Save protection rules**.

Now update the workflow's frontmatter to run inside that environment:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 9 * * 1-5"
jobs:
  report:
    runs-on: ubuntu-latest
    environment: agentic-production
    permissions:
      issues: write
      contents: read
---
```

When the workflow triggers, GitHub pauses the job and notifies the designated reviewers. The run only proceeds after an approval.

> [!NOTE]
> Required reviewers add a manual gate. Use them for workflows that can open issues, comment on pull requests, or push commits — not for read-only reporting workflows.

### Store shared secrets at the org level

Instead of adding `COPILOT_GITHUB_TOKEN` or third-party API keys to every repository individually, store them once at the organisation level:

1. Go to your organisation → **Settings → Secrets and variables → Actions**.
2. Click **New organisation secret**.
3. Enter the secret name (for example `COPILOT_GITHUB_TOKEN`).
4. Under **Repository access**, choose **Selected repositories** and add only the repos that need it.
5. Click **Add secret**.

Workflows in selected repositories can reference `${{ secrets.COPILOT_GITHUB_TOKEN }}` without any per-repo setup.

> [!TIP]
> Scope org secrets to **Selected repositories**, not **All repositories**. Granting every repository access to a privileged token widens your blast radius if any workflow is misconfigured.

### Audit who can change workflow files

Protect the `.github/workflows/` directory with a **CODEOWNERS** rule so any change to a workflow file requires review from a trusted team:

1. Open (or create) `.github/CODEOWNERS` in your repository.
2. Add a rule that assigns ownership to a security or platform team:

```
.github/workflows/  @my-org/platform-team
```

1. Commit and push the change.

Now a pull request that modifies any workflow file automatically requests a review from `@my-org/platform-team` before it can be merged.

<details>
<summary>🖥️ GitHub UI alternative — add CODEOWNERS</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. Set the path to `.github/CODEOWNERS`.
3. Paste your ownership rules into the editor.
4. Click **Commit new file**.

</details>

## ✅ Checkpoint

- [ ] Your organisation's default workflow permissions are set to read-only
- [ ] You created an `agentic-production` environment with at least one required reviewer
- [ ] At least one workflow's frontmatter references the `agentic-production` environment
- [ ] A shared secret is stored at the org level and scoped to selected repositories only
- [ ] A `CODEOWNERS` rule requires platform-team review for changes to `.github/workflows/`

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

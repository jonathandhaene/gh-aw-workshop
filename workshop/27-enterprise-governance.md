<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Enterprise teams need more than working workflows — they need confidence that every workflow runs with the right permissions, passes the right approvals, and stays within policy._

## 🎯 What You'll Do

Apply GitHub organisation-level controls to your agentic workflows: set required reviewers before a workflow runs, scope permissions to the minimum required, and confirm your GHEC or GHES policy settings allow the Copilot agent to operate safely across your repository fleet.

## 📋 Before You Start

- You have a scheduled agentic workflow running from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have cost guardrails in place from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin or organisation owner rights, or are following along with read-only access.

> [!NOTE]
> This step covers GitHub Enterprise Cloud (GHEC) and GitHub Enterprise Server (GHES) features. Free or Team plan users can read for context.

## Steps

### Understand why governance matters

When an agentic workflow runs, it acts on your behalf — it can open issues, post comments, and (if you grant it) push code. In a multi-team organisation, it matters who authorised the workflow and what it can touch.

Three levers control this:

| Lever | What it controls |
|---|---|
| **`permissions:` frontmatter** | Which GitHub API scopes the `GITHUB_TOKEN` carries for this run |
| **Required reviewers (Environments)** | Whether a human must approve before the workflow job runs |
| **Organisation Actions policy** | Which workflows are allowed to run in your org at all |

### Audit your workflow permissions

Open `.github/workflows/daily-status.md`. Locate the `permissions:` block. A minimal read-and-report workflow only needs:

```yaml
permissions:
  contents: read
  issues: write
```

Remove any unused scopes — `contents: write`, `pull-requests: write`, and `actions: write` are not needed for a reporting-only workflow.

> [!TIP]
> A narrower `permissions:` block limits the blast radius if a prompt-injection attack triggers unintended writes. See [Side Quest: Permission Escalation in Agentic Workflows](side-quest-17-04-permission-escalation.md).

Compile and push after editing:

```bash
gh aw compile
git add .github/workflows/daily-status.md .github/workflows/daily-status.lock.yml
git commit -m "chore: tighten workflow permissions to minimum required"
git push
```

### Add a required reviewer with GitHub Environments

A [GitHub Environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) lets you gate a workflow job behind a manual approval.

1. Go to **Settings → Environments → New environment** and name it `agentic-approval`.
2. Under **Protection rules**, enable **Required reviewers** and add yourself or a team.
3. Click **Save protection rules**.
4. Reference the environment in your workflow frontmatter:

```yaml
jobs:
  daily-status:
    environment: agentic-approval
    runs-on: ubuntu-latest
```

Each run now pauses until a reviewer clicks **Approve and deploy** in the Actions tab.

> [!NOTE]
> Required reviewers add most value for workflows that write code or open PRs. For a read-and-report workflow, use your judgement.

### Review your organisation's Actions and Copilot policies

In your organisation's **Settings → Actions → General** page, confirm:

- **Workflow permissions** defaults to **Read repository contents** (not write).
- **Allow GitHub Actions to create and approve pull requests** is disabled.
- Under **Copilot → Policies**, **Copilot in GitHub Actions** is enabled for your repositories.

On GHES, find these settings in the **Admin console** under **Policies → Actions** and **Policies → Copilot**.

## ✅ Checkpoint

- [ ] You reviewed and tightened the `permissions:` block in at least one workflow
- [ ] You created an `agentic-approval` environment with at least one required reviewer
- [ ] You located the org Actions policy page (or confirmed settings with your admin)
- [ ] You confirmed that Copilot in GitHub Actions is enabled for your repository
- [ ] You can explain the difference between `permissions:` frontmatter and org-level Actions policies

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

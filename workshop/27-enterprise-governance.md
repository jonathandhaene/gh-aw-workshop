<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Enterprise teams need more than budgets — they need consistent policies for who can run AI workflows, what those workflows can touch, and how changes get reviewed before they ship._

## 🎯 What You'll Do

Set up organisation-level controls that make agentic workflows safe to run at scale. You'll configure required reviewers for workflow deployments, scope Copilot access to the right repositories, and verify that your organisation's policy settings match your team's risk posture.

## 📋 Before You Start

- You have a scheduled workflow running from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You've set spending limits from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have Owner or Admin access to the organisation (or a sandbox org to practise in).

## Steps

### Review the three governance levers

GitHub gives you three independent controls over agentic workflow behaviour:

| Lever | Where to set it | What it controls |
|---|---|---|
| Copilot policy | Org **Settings → Copilot → Policies** | Which repositories may call Copilot from Actions |
| Environment protection | Repo **Settings → Environments** | Who must approve a deployment before the workflow job starts |
| Actions permissions | Org **Settings → Actions → General** | Which workflow files and external actions are allowed to run |

Start with the Copilot policy — it is the fastest win.

### Scope Copilot access to approved repositories

1. Go to your organisation on GitHub and open **Settings**.
2. Select **Copilot** → **Policies** in the left sidebar.
3. Under **GitHub Actions access**, choose **Selected repositories** instead of **All repositories**.
4. Add only the repositories that contain reviewed, approved agentic workflow files.

This prevents a new repository from running Copilot-powered jobs until an admin explicitly approves it.

> [!TIP]
> On GitHub Enterprise Cloud, the same setting is available at the enterprise level under **Enterprise Settings → Copilot → Policies**. Enterprise-level policy overrides org-level policy when both are present.

### Add a required reviewer environment for workflow deployments

Use a GitHub Environment to gate any workflow that writes to production resources:

1. Open the repository's **Settings → Environments** and click **New environment**.
2. Name it `production-ai` (or whatever matches your team's naming convention).
3. Enable **Required reviewers** and add the people or teams who should approve each run.
4. In your workflow frontmatter, reference the environment:

```yaml
---
name: daily-status
on:
  schedule:
    - cron: "0 8 * * 1-5"
jobs:
  report:
    runs-on: ubuntu-latest
    environment: production-ai
    steps:
      - uses: github/gh-aw@v1
---
```

Now every scheduled run pauses at the `environment: production-ai` gate until a reviewer approves it. Urgent workflows can be approved in seconds; non-critical ones can wait for a scheduled review window.

### Restrict which Actions and workflows can run

1. In org **Settings → Actions → General**, under **Actions permissions**, select **Allow select actions and reusable workflows**.
2. Add `github/gh-aw@*` to the allow-list. Remove any overly broad wildcard entries.
3. Save, then trigger a test run to confirm the restriction takes effect without blocking legitimate workflows.

> [!NOTE]
> On GHES, the allow-list lives at the enterprise level. Ask your site administrator whether org-level overrides are permitted.

## ✅ Checkpoint

- [ ] Copilot Actions access is scoped to **selected repositories** (not all repositories) in org settings
- [ ] You created a `production-ai` environment with at least one required reviewer
- [ ] Your workflow frontmatter references the environment with `environment: production-ai`
- [ ] `github/gh-aw@*` is on the Actions allow-list and broader wildcards are removed
- [ ] You triggered a test run and confirmed the reviewer gate appeared as expected
- [ ] You know which of these controls are set at the enterprise level versus the org level in your environment

<!-- journey: all -->
Want to explore more advanced topics? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

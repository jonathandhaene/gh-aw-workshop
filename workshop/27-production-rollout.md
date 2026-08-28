<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Deploy Your Agentic Workflow to Production

> _Getting a workflow running in a personal repository is satisfying — but shipping it safely to a team or organisation is where the real impact begins._

## 🎯 What You'll Do

You'll apply a lightweight production readiness checklist to your workflow, configure the minimum permissions it needs, and understand the key policies your GitHub administrator will expect you to follow before wider rollout.

By the end of this step your workflow will be ready to share with your team — with guardrails that protect your organisation's data and budget.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) — especially the `max-ai-credits` and `timeout-minutes` settings.
- You have access to your organisation or enterprise GitHub settings (or can contact an admin).

## Apply the production readiness checklist

Work through each item below before enabling the workflow for your team.

### Lock down permissions

Follow the principle of least privilege. In your workflow frontmatter, declare only the permissions the agent genuinely needs:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  issues: write
  contents: read
---
```

Remove any permission you added during development but no longer use. If your workflow only reads issues and posts comments, it does not need `contents: write`.

### Review safe outputs

Check every [safe output](https://github.github.com/gh-aw/reference/safe-outputs/) your workflow uses. Ask yourself:

- Could the agent produce output that exposes internal data (e.g., security issue details, private user names)?
- Is each output written to the intended target (issue, PR, repository file)?

If the answer to the first question is "possibly yes," add scoping instructions to your agent brief — for example: _"Do not include individual contributor names in the summary."_

### Confirm organisation policy allows Copilot agents

Your GitHub administrator must have enabled the Copilot cloud agent for the organisation. On **GitHub Enterprise Cloud (GHEC)** or **GitHub Enterprise Server (GHES) 3.12+**, this setting lives under **Organisation settings → Copilot → Policies**.

> [!NOTE]
> If you are on GHES and unsure, check [Side Quest: Enterprise Setup Considerations](side-quest-enterprise-setup.md) before proceeding.

<details>
<summary>🖥️ How to check the policy in the GitHub UI</summary>

1. Go to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Confirm that **Allow Copilot to complete agentic tasks in Actions** is set to **Enabled**.
4. If the setting is absent, the feature is not yet available on your GHES version.

</details>

### Set a sensible schedule (avoid over-triggering)

Scheduled workflows run on every matching event, so an overly broad cron expression multiplies costs and noise. For a daily status report, five runs per week (weekdays only) is usually the right balance:

```yaml
on:
  schedule:
    - cron: "0 8 * * 1-5"
```

Avoid `* * * * *` (every minute) or other high-frequency schedules during the testing phase — a single typo can cause hundreds of unwanted runs.

### Pin your model

In production, pin the model so a default change cannot alter your workflow's output quality without your awareness:

```yaml
---
model: copilot/gpt-4o
---
```

Check the [supported models reference](https://github.github.com/gh-aw/reference/engines/) periodically and update intentionally.

### Test in a staging repository first

Before rolling out to your team repository, run the workflow once more in a personal or throwaway repository. Confirm:

1. The run completes without errors.
2. The safe output (issue comment, file commit, etc.) looks exactly as intended.
3. AIC consumption matches your forecast from the previous step.

## Enable for your team

Once you have ticked off the checklist above:

1. Move (or re-create) the workflow file in your team's repository under `.github/workflows/`.
2. Commit and push — GitHub Actions picks it up immediately.
3. Trigger a manual run from **Actions → your workflow → Run workflow** to confirm it works in the new context.
4. Share the workflow file with your team in a PR description or team channel, including a brief note on what it does and how to adjust the schedule or permissions if needed.

## ✅ Checkpoint

- [ ] Your workflow frontmatter declares only the permissions it genuinely needs
- [ ] You reviewed every safe output and confirmed it does not expose unintended data
- [ ] You confirmed your organisation policy allows Copilot cloud agents
- [ ] Your `cron` schedule is set to a sensible frequency (not every minute)
- [ ] You have pinned the model in frontmatter
- [ ] You ran a final test in a staging repository and the output looked correct
- [ ] The workflow file is committed to the target team repository

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

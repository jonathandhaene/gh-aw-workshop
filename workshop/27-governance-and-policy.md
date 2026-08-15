# Govern Your Agentic Workflows at the Organisation Level

> _Setting individual budgets is a good start — but sustainable AI automation requires consistent policy across every team and repository in your organisation._

## 🎯 What You'll Do

Apply organisation-level controls to your agentic workflows: set concurrency limits so parallel runs don't exhaust your AI credit quota all at once, lock down which runners workflows may target, and verify that your workflows comply with your organisation's policy settings. By the end, you'll understand the levers an admin or workflow author can pull to keep AI automation reliable and auditable at scale.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have a working agentic workflow in your repository (for example, `daily-status.md`).
- You have Owner or Admin access to your organisation, **or** you are following along with read-only access and will note the settings to share with your admin.

> [!NOTE]
> If you are on GitHub Enterprise Server (GHES) or GitHub Enterprise Cloud (GHEC), your organisation administrator may have already applied some of these settings centrally. Check with your admin before overriding anything at the repository level.

## Steps

### Understand the two layers of governance

Agentic workflow governance works at two levels:

| Layer | Who controls it | Where it lives |
|---|---|---|
| **Organisation policy** | Org Owner / Enterprise admin | Organisation Settings → Actions → General |
| **Workflow author controls** | You, the workflow author | Frontmatter of each `.md` workflow file |

Organisation policy sets hard limits that workflow authors cannot override. Workflow author controls add tighter restrictions within those limits. Think of it as an outer fence and an inner fence — the agent can only operate in the space enclosed by both.

### Review organisation-level Actions policies

Open your organisation settings and confirm these three settings are configured appropriately:

1. In GitHub, click your organisation name, then **Settings** → **Actions** → **General**.
2. Under **Actions permissions**, verify that agentic workflows are allowed for your repositories. For most organisations, **Allow all actions and reusable workflows** is the right setting while you are learning. Enterprise teams often restrict this to actions from approved sources.
3. Under **Workflow permissions**, set the default token permission to **Read repository contents and packages permissions**. This ensures all new workflows start from a least-privilege baseline.
4. Note the **Maximum workflow run time** setting. If your agentic workflows exceed this, they will be cancelled — add a matching `timeout-minutes` to your frontmatter to fail fast and informatively instead.

![Organisation Actions settings showing permissions and workflow runtime controls](images/27-org-actions-settings.png)

### Set concurrency limits to prevent credit exhaustion

When multiple runs of the same workflow overlap — for example, because a push trigger fires several times in quick succession — they each consume AI credits simultaneously. Use a `concurrency` group to queue them instead of letting them pile up.

Add a `concurrency` block to your workflow frontmatter:

```yaml
---
name: Daily Repository Status
on:
  schedule: daily on weekdays
  workflow_dispatch:

concurrency:
  group: daily-status-${{ github.ref }}
  cancel-in-progress: false

permissions:
  contents: read

max-ai-credits: 500
max-daily-ai-credits: 1500
---
```

The `cancel-in-progress: false` setting queues new runs rather than cancelling the active one — appropriate for a reporting workflow where you want every run to complete. For interactive workflows (like PR reviewers) where a stale run is useless, use `cancel-in-progress: true` instead.

<details>
<summary>🖥️ GitHub UI alternative</summary>

You can edit the workflow frontmatter directly in the GitHub web editor:

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `concurrency:` block to the frontmatter between the `---` fences.
4. Click **Commit changes**.

Remember: committing via the web editor does **not** automatically recompile the lock file. Open your Codespace or local terminal and run `gh aw compile`, then push the updated `.lock.yml`.

</details>

### Validate your compiled workflow

After editing, recompile and confirm the concurrency setting appears in the lock file:

```bash
gh aw compile
grep -A 3 "concurrency" .github/workflows/daily-status.lock.yml
```

You should see the `concurrency` block preserved in the compiled output.

### Review rate-limiting controls (enterprise teams)

If your organisation uses GitHub Enterprise Cloud or GitHub Enterprise Server, your enterprise administrator can set rate-limiting controls that cap AI model calls across all workflows in the enterprise. These controls appear in **Enterprise Settings** → **Copilot** → **Policies**.

As a workflow author, you can work within those limits by:

- Keeping your agent brief focused and concise to reduce token use per run.
- Using `max-ai-credits` to ensure a single runaway workflow cannot consume the full enterprise quota.
- Scheduling long-running or expensive workflows during off-peak hours to avoid contention with interactive developer use of Copilot.

> [!TIP]
> Ask your enterprise admin for the per-organisation and per-repository AIC quota if you are unsure of your headroom. Knowing the quota early prevents surprises when your workflows scale up.

### Document your governance decisions

A brief comment at the top of each workflow file makes your policy choices auditable and easy to hand off to teammates or a future admin:

```yaml
---
# Governance: concurrency group prevents parallel credit exhaustion.
# Budget: 500 AIC/run, 1500 AIC/day. Adjust if usage grows.
# Runner: GitHub-hosted; see 24-self-hosted-runners.md for GHES override.
name: Daily Repository Status
# ... rest of frontmatter
---
```

This comment costs nothing and answers the first question every reviewer will ask: "Why is this configured this way?"

## ✅ Checkpoint

- [ ] You located the Actions permissions and workflow permissions settings in your organisation settings
- [ ] You added a `concurrency` group to your workflow frontmatter and chose an appropriate `cancel-in-progress` value
- [ ] You ran `gh aw compile` and confirmed the `concurrency` block appears in the compiled `.lock.yml`
- [ ] You can explain the difference between organisation-level policy limits and workflow-author controls
- [ ] You added a brief governance comment to your workflow frontmatter explaining the concurrency and budget choices

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

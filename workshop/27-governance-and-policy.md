<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern and Control Your Agentic Workflows

> _Enterprise teams need more than a running workflow — they need confidence that workflows run within defined boundaries and can be audited by anyone who asks._

## 🎯 What You'll Do

You will apply the three levers of agentic workflow governance: **required reviewers** (humans approve before a workflow writes anything sensitive), **model policy** (pin the model your organisation has approved), and **permissions scoping** (request only the access each workflow truly needs). When you finish, your workflow will have a clear, reviewable policy posture.

## 📋 Before You Start

- Your workflow runs successfully (see [Test and Iterate on Your Workflow](12-test-and-iterate.md)).
- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) — governance builds on the audit trail you set up there.
- You understand the [permissions block](https://github.github.com/gh-aw/reference/permissions/) in agentic workflow frontmatter.

## Steps

### Review your workflow's permissions

Open your `daily-status.md` workflow file. Look for the `permissions:` block near the top.

If your file does not have one yet, the workflow inherits the runner's default permissions. That is fine for exploration, but enterprise policy usually requires an explicit declaration.

Add or update the block so it lists only what your workflow actually needs:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
---
```

Keep each permission at the lowest level that still lets the workflow do its job. `read` is almost always enough for data-gathering steps; `write` should only appear for the one resource the workflow posts to.

> [!TIP]
> `gh aw audit` shows which permissions a previous run actually exercised. Use that output to trim any permissions you declared but never used.

### Pin the model

Your organisation's security or legal team may require that only approved models process your repository data. Lock the model in frontmatter so no run silently upgrades to an unapproved version:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
model: gpt-4o
---
```

Replace `gpt-4o` with the model identifier your organisation has approved. Check [the engines reference](https://github.github.com/gh-aw/reference/engines/) for accepted identifiers.

> [!NOTE]
> If your enterprise uses a specific Copilot model policy, set `model:` to match it. Runs that request an un-approved model will fail at compile time, not at runtime — a fast, safe failure.

### Add a required reviewer for sensitive outputs

Some workflows post to issues, open pull requests, or send Slack notifications. Before that output goes out, a human reviewer can check it.

Add a `reviewers:` block to require approval before any safe output fires:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
model: gpt-4o
reviewers:
  - your-github-username
---
```

When `reviewers:` is present, gh-aw pauses after the agent finishes reasoning and posts a draft of every planned safe output. The listed reviewers receive a notification and approve or reject the run in the GitHub Actions UI before anything is written.

### Compile and push your policy changes

After editing the frontmatter, recompile to regenerate the lock file:

```bash
gh aw compile
```

Then commit and push both `daily-status.md` and the updated `daily-status.lock.yml`.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Update the `permissions:`, `model:`, and `reviewers:` fields.
4. Click **Commit changes**.
5. Because `gh aw compile` is terminal-only, push your changes and let GitHub Actions re-run the workflow to surface any lock-file drift as a workflow error. Fix locally with `gh aw compile` if an error appears.

</details>

### Verify the policy is in effect

Trigger a manual run with `workflow_dispatch`. In the Actions log, confirm:

- The run header shows the pinned model name.
- If you added `reviewers:`, the run pauses at the **Awaiting Review** gate before posting output.

Approve the run (or ask a teammate to do so). The workflow should complete normally.

## ✅ Checkpoint

- [ ] Your `daily-status.md` has an explicit `permissions:` block with least-privilege values
- [ ] You set `model:` to a specific, organisation-approved model identifier
- [ ] You added at least one entry to `reviewers:` (or you can explain why your workflow doesn't need one)
- [ ] `gh aw compile` passed with no errors after your frontmatter changes
- [ ] `daily-status.lock.yml` was regenerated and committed alongside the `.md` file
- [ ] A manual run triggered and the Actions log confirmed the pinned model was used
- [ ] If `reviewers:` is set, the run paused at the review gate before writing any output
- [ ] You can explain, in one sentence, why least-privilege permissions reduce enterprise risk

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

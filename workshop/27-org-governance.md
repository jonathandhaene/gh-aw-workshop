<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Running AI-powered workflows at scale means deciding who can run them, what they can write, and when a human needs to approve the result._

## 🎯 What You'll Do

You'll apply three governance controls to your workflow: **staged mode** to gate agent writes behind human approval, **protected files** to declare off-limits paths, and **concurrency** settings to prevent overlapping runs. Together these controls give teams confidence to run agentic workflows at production scale.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You can edit your workflow's frontmatter (the YAML block between the `---` fences at the top of your `.github/workflows/<name>.md` file).

## Steps

### Understand the three governance levers

| Control | What it does | When to use it |
|---------|-------------|----------------|
| `staged-mode` | The agent drafts its output but waits for a human to approve before writing | Any workflow that creates or updates files, issues, or pull requests |
| `protected-files` | Blocks the agent from modifying listed paths entirely | Sensitive configs, CODEOWNERS, secrets, lockfiles |
| `concurrency` | Prevents two runs from writing to the same resource at the same time | Scheduled workflows and any workflow triggered by push or PR |

### Enable staged mode

**Staged mode** means the agent runs its full reasoning and drafts every write operation, then pauses. A reviewer sees a summary of the proposed writes, approves or rejects them, and only then are the writes applied.

Add `staged-mode: true` to your workflow frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  contents: write
  issues: write
staged-mode: true
---
```

> [!TIP]
> Staged mode is especially valuable in enterprise environments where a workflow touches production issues or creates pull requests on behalf of a team. It lets you run the agent confidently while keeping a human in the loop.

After enabling staged mode, trigger a manual run from the **Actions** tab. Instead of writing immediately, the run will pause and show a **Review pending writes** banner. Click **Approve** to apply the writes, or **Reject** to discard them.

<details>
<summary>🖥️ Approving a staged run in the GitHub UI</summary>

1. Go to your repository on GitHub and click the **Actions** tab.
2. Select the paused workflow run — it will show a yellow dot and a **Waiting for approval** label.
3. Click **Review pending writes** to see the list of proposed changes.
4. Click **Approve writes** to confirm, or **Reject** to discard the entire run.

</details>

### Protect sensitive files

Use `protected-files` to declare paths the agent may never modify, regardless of its task brief. This is enforced at the platform level — the agent's write tools simply fail if they target a listed path.

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  contents: write
  issues: write
staged-mode: true
protected-files:
  - .github/workflows/*.yml
  - .github/workflows/*.md
  - CODEOWNERS
  - package-lock.json
---
```

Glob patterns are supported. The example above protects all workflow definition files — so even if an injected prompt asks the agent to modify its own workflow, the platform blocks it.

### Set concurrency controls

Without concurrency controls, two scheduled runs that overlap (common if a run takes longer than expected) can both attempt to open the same issue or update the same file simultaneously, causing conflicts.

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  contents: write
  issues: write
staged-mode: true
protected-files:
  - .github/workflows/*.yml
  - CODEOWNERS
concurrency:
  group: daily-status-report
  cancel-in-progress: false
---
```

Setting `cancel-in-progress: false` means a new run queues behind the running one rather than cancelling it. For a daily status report this is usually correct — both days' reports should eventually be written. Flip to `true` for workflows where only the most recent result matters (for example, a PR diff summary).

### Compile and push your changes

After editing the frontmatter, compile to regenerate the lock file:

```bash
gh aw compile
```

Then commit and push both the `.md` file and the updated `.lock.yml`:

```bash
git add .github/workflows/
git commit -m "chore: add staged-mode, protected-files, and concurrency governance"
git push
```

<details>
<summary>🖥️ GitHub UI alternative for committing changes</summary>

1. Navigate to your workflow `.md` file in the repository.
2. Click the **pencil icon (✏️)** to edit.
3. Update the frontmatter with the new fields.
4. Click **Commit changes** — describe the change in the commit message.
5. Open a Codespace or terminal to run `gh aw compile` and push the regenerated `.lock.yml`.

</details>

> [!NOTE]
> `gh aw compile` is a terminal-only step. If you are on a UI-only path, commit the `.md` change and let the Actions workflow run — the lock file will be regenerated automatically if your repository has a compile step wired up, or you can revisit this after opening a Codespace.

## ✅ Checkpoint

- [ ] You added `staged-mode: true` to your workflow frontmatter and triggered a run
- [ ] You approved or rejected the staged run using the **Review pending writes** UI
- [ ] You added at least two paths to `protected-files` in your workflow frontmatter
- [ ] You added a `concurrency` block with an appropriate `group` name and `cancel-in-progress` value
- [ ] You compiled the updated workflow and pushed both the `.md` and `.lock.yml` files

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

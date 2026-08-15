# Govern Your Agentic Workflows at Scale

> _One workflow that works great is a proof of concept — a fleet of well-governed workflows is a production system._

## 🎯 What You'll Do

You'll apply three governance levers to your agentic workflow: a required approval gate for sensitive writes, a branch-protection rule to prevent direct pushes to `main`, and a `protected-files` declaration that stops the agent from overwriting files it should never touch.
By the end, you'll have a repeatable governance pattern you can roll out across any repository.

## 📋 Before You Start

- You have a working agentic workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- You understand how `permissions:` and `safe-outputs` work — review [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) if needed.

## Steps

### Understand the governance model

`gh-aw` applies governance at three layers:

| Layer | What it controls |
|-------|-----------------|
| `permissions:` | Which GitHub API scopes the agent can use |
| `safe-outputs` | Which write operations the agent can perform |
| `protected-files` | Which file paths the agent must never modify |

Together these enforce the **principle of least authority**: the agent can only do exactly what the task requires.

### Add a `protected-files` declaration

Open your workflow file (for example `.github/workflows/daily-status.md`) and add a `protected-files` block to the frontmatter.

**GitHub UI path (preferred):**

1. Navigate to `.github/workflows/daily-status.md` in your repository.
2. Click the **pencil icon (✏️)** to open the editor.
3. Inside the opening `---` frontmatter fence, add these lines:

```yaml
protected-files:
  - ".github/workflows/**"
  - "CODEOWNERS"
  - ".github/CODEOWNERS"
```

1. Click **Commit changes**.

<details>
<summary>🖥️ Terminal alternative</summary>

Edit the file locally and commit:

```bash
git add .github/workflows/daily-status.md
git commit -m "chore: add protected-files governance"
git push
```

</details>

### Require a pull request for agent writes

Instead of letting the agent push directly, configure it to open a pull request. In the `safe-outputs` block of your frontmatter, replace any `push` or `commit` output type with `create-pull-request`:

```yaml
safe-outputs:
  - create-pull-request
```

Now every proposed write becomes a reviewable PR — a human must approve it before it merges.

### Set a branch protection rule

1. In your repository, go to **Settings → Branches**.
2. Click **Add branch protection rule**.
3. Set **Branch name pattern** to `main`.
4. Check **Require a pull request before merging**.
5. Optionally check **Require approvals** and set the count to **1**.
6. Click **Create**.

![Branch protection rule settings](images/27-branch-protection.png)

> [!TIP]
> Enterprise repositories on GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) can enforce these rules at the organisation level using **Rulesets** under **Settings → Rules → Rulesets**.

### Compile and verify

If you have a terminal available, regenerate the lock file so the new frontmatter takes effect:

```bash
gh aw compile
```

If you are working entirely in the GitHub UI, push your changes and let Actions pick them up on the next trigger.

## ✅ Checkpoint

- [ ] Your workflow frontmatter includes a `protected-files` block covering at least your `.github/workflows/` directory
- [ ] Your `safe-outputs` block uses `create-pull-request` rather than a direct push type
- [ ] A branch protection rule (or Ruleset) on `main` requires pull request review before merging
- [ ] You can describe what would happen if the agent tried to overwrite a protected file
- [ ] You understand the difference between `permissions:` (API scope) and `protected-files` (path guard)

**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose another path, or visit the [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/) for the full enterprise policy reference.

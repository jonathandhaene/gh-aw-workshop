# Govern Agentic Workflows Across Your Organisation

> _When one workflow becomes ten, you need consistent policies — not heroic manual reviews._

## 🎯 What You'll Do

You'll apply organisation-level controls to your agentic workflows: standardise permissions across repos, enforce concurrency limits for the org, and use GitHub's policy settings to decide which workflows can run at all. By the end, you'll have a mental model for governing agentic workflows in an enterprise environment.

## 📋 Before You Start

- You've completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You understand `permissions:` frontmatter and `safe-outputs` from earlier steps.
- You have at least one workflow (`daily-status.md`) pushed to your repository.

## Review the four layers of enterprise governance

Governance operates at four levels. Each layer narrows what a workflow can do.

| Layer | Where it lives | What it controls |
|---|---|---|
| **GitHub org policy** | Organisation → Settings → Actions | Which repos can run Actions; which Actions or Copilot models are allowed |
| **Workflow frontmatter** | `daily-status.md` | Permissions, safe-outputs, credit limits, timeout, concurrency |
| **Repository Ruleset** | Repository → Settings → Rules | Branch protection, required status checks |
| **CODEOWNERS / protected files** | `protected-files:` frontmatter | Which files the agent may not write |

You control layers 2–4 directly. Layer 1 is set by your GitHub admin, but knowing it exists lets you ask the right questions.

> [!NOTE]
> On GitHub Enterprise Server (GHES), organisation policy settings may be managed at the enterprise level rather than per-org. Ask your admin whether workflow policies are set in **Enterprise → Policies → Actions** instead.

## Enforce least-privilege permissions

Every workflow should request only the access it genuinely needs. Open `daily-status.md` and confirm your `permissions:` block follows this pattern:

```yaml
permissions:
  contents: read
  issues: write
```

Give `write` only to the resource your workflow actually modifies (issues, pull requests, or repository contents). If your workflow only reads the repo, use `contents: read` and nothing else.

> [!TIP]
> Use `protected-files:` to prevent the agent from touching sensitive paths even if `contents: write` is set. For example:
>
> ```yaml
> protected-files:
>   - ".github/workflows/**"
>   - "CODEOWNERS"
> ```

## Add a concurrency group

If the same workflow can be triggered multiple times in quick succession — for example, from push events or manual dispatches — add a `concurrency:` block to prevent overlapping runs from colliding:

```yaml
concurrency:
  group: daily-status-${{ github.ref }}
  cancel-in-progress: true
```

`cancel-in-progress: true` cancels the older queued run when a new one starts. For workflows where you want to queue rather than cancel, set it to `false`.

<details>
<summary>🖥️ Add concurrency via the GitHub UI</summary>

1. Navigate to your workflow file (`daily-status.md`) in your repository on GitHub.
2. Click the **pencil icon (✏️)** to open the editor.
3. Add the `concurrency:` block to the frontmatter, between the `---` fences.
4. Scroll to the bottom and click **Commit changes**.
5. After committing, recompile locally with `gh aw compile` or let the next triggered run pick up the updated `.lock.yml`.

</details>

## Apply network restrictions

Agentic workflows can make outbound HTTP requests through MCP tools or the agent itself. Restrict the domains your workflow may reach:

```yaml
network:
  allowed-domains:
    - api.github.com
    - your-internal-tool.example.com
```

Limiting `allowed-domains` prevents a misdirected agent from reaching unexpected external services. On GHES, you may also need to add your internal GitHub hostname.

## Check org-level Actions policy

Ask your GitHub admin (or check yourself if you have org admin rights):

1. Go to **Your organisation → Settings → Actions → General**.
2. Under **Policies**, confirm whether Actions are restricted to a specific set of workflows or allowed broadly.
3. Under **Copilot**, confirm whether the Copilot models used by agentic workflows are permitted for your organisation.

If a workflow fails to start with a policy error rather than a runtime error, the fix is at layer 1 — not in your workflow file.

> [!NOTE]
> GHEC organisations may have enterprise-level policies that override org-level settings. If org settings appear greyed out, they are controlled at the enterprise tier.

## ✅ Checkpoint

- [ ] You can name the four governance layers and say who controls each one
- [ ] Your workflow's `permissions:` block uses least-privilege — only the scopes it actually needs
- [ ] You added a `protected-files:` block (or confirmed one is not needed) in your workflow
- [ ] You added a `concurrency:` group to your workflow frontmatter
- [ ] You added a `network.allowed-domains` list to your workflow
- [ ] `gh aw compile` completed without errors after your changes
- [ ] You located the Actions policy page in your organisation (or know who to ask)
- [ ] You can explain the difference between a policy error and a runtime error in a workflow log

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

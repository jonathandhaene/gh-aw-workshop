<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows at Scale

> _When agentic workflows run across teams, you need guardrails that prevent accidents — not just policies in a wiki._

## 🎯 What You'll Do

You will apply three built-in enforcement mechanisms — `protected-files`, `network.allowed-domains`, and `safe-outputs` surface declarations — to harden a workflow for shared or enterprise use. You will also explore required workflow approvals so that no agentic workflow runs in your organisation without an explicit sign-off.

## 📋 Before You Start

- You have a working agentic workflow deployed in your repository (see [Run and Watch Your Workflow](08-run-your-workflow.md)).
- You understand `permissions:` and `safe-outputs:` basics (see [Write Your First Agentic Workflow](07-your-first-workflow.md)).
- You are comfortable reading and editing workflow frontmatter YAML.

## Steps

### Protect sensitive files from accidental agent writes

The `protected-files:` key in workflow frontmatter tells the gh-aw runtime to treat a path pattern as read-only for the agent, even if `contents: write` is declared in `permissions:`.

Add it directly under your `permissions:` block:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: write
  issues: write
protected-files:
  - ".github/workflows/**"
  - "CODEOWNERS"
  - "*.lock"
safe-outputs:
  - create-issue
  - add-comment
---
```

This pattern means the agent can write issues and comments, but it can never overwrite your workflow files, ownership declarations, or lock files — even if a prompt injection attempts to do so.

> [!TIP]
> Start with `".github/**"` to blanket-protect all configuration files, then loosen individual exceptions only as needed.

### Lock outbound network access

By default, an agentic workflow can call any HTTPS endpoint that `GITHUB_TOKEN` authorises. The `network.allowed-domains:` block tightens this to an explicit allowlist:

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: write
network:
  allowed-domains:
    - api.github.com
    - github.com
safe-outputs:
  - create-issue
---
```

Any outbound call to a domain not on the list is blocked at the sandbox level and appears as a `[firewall: blocked]` log line — visible in `gh aw audit`. This single control eliminates most data-exfiltration paths.

> [!NOTE]
> On GitHub Enterprise Server, the `api.github.com` entry should be replaced with your GHES hostname. Ask your GitHub administrator for the correct value.

### Declare exactly what the agent may write

Listing surface names in `safe-outputs:` is the strongest write control available. Each surface name restricts the agent to one class of write operation. The agent cannot create a pull request if only `create-issue` is declared — regardless of what the task brief says.

Common surface names and what they allow:

| Surface | What it permits |
|---------|----------------|
| `create-issue` | Open a new issue |
| `add-comment` | Post a comment on an issue or pull request |
| `create-pull-request` | Open a new pull request |
| `push-to-pull-request-branch` | Commit to an existing PR branch |

Use the narrowest set your workflow actually needs. A daily status workflow that only posts issue comments needs exactly `add-comment` — nothing else.

### Enable required workflow approvals

For workflows that write to production repositories or post public-facing content, enable required approvals so every run is reviewed before it executes.

1. In your repository, go to **Settings** → **Actions** → **General**.
2. Under **Fork pull request workflows**, choose **Require approval for all outside collaborators**.
3. For internal teams on GitHub Enterprise Cloud, consider **Environment protection rules**: create an environment named `production`, add required reviewers, and reference it in your workflow's `jobs.<id>.environment:` field.

With an environment gate, the workflow queues the run and notifies reviewers. No agent code executes until at least one required reviewer approves.

### Review org-level policies (enterprise)

GitHub Enterprise administrators can enforce workflow policies across all repositories in an organisation:

- **Required workflows**: enforce that a specific governance workflow runs on every repository before a merge is allowed.
- **Allowed actions and reusable workflows**: restrict which actions can be called, preventing unapproved third-party actions from running alongside agentic steps.
- **Audit log streaming**: stream all Actions events to a SIEM so agentic workflow runs are part of your centralised security record.

These controls live under **GitHub Enterprise** → **Organisation settings** → **Actions**. Work with your GitHub administrator to align them with your organisation's policy baseline.

## ✅ Checkpoint

- [ ] You added `protected-files:` to your workflow frontmatter and verified the patterns cover your sensitive paths
- [ ] You added `network.allowed-domains:` and confirmed the allowlist contains only the domains your workflow actually calls
- [ ] Your `safe-outputs:` block lists the minimum surfaces the workflow needs — no extras
- [ ] You can explain the difference between `permissions:`, `protected-files:`, and `safe-outputs:` to a colleague
- [ ] You know where to find required workflow approval settings in your repository or organisation

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

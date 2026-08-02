<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Agentic workflows with write access are powerful enough to demand clear, consistent governance — the same accountability your team expects from any automated system._

## 🎯 What You'll Do

You'll apply a set of org-level controls that keep agentic workflows trustworthy in a shared environment: required code-review on workflow files, branch protection on the lock file, `protected-files` declarations, and a simple policy template your team can reference. By the end you'll have a governance checklist you can hand to a security or compliance reviewer.

## 📋 Before You Start

- You've completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) and [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to the repository or GitHub organization, **or** you are following along with read access and noting the steps to share with your administrator.

## Steps

### Understand why governance matters for agentic workflows

A standard GitHub Actions workflow runs deterministic code: every push produces the same output. An agentic workflow runs an AI model that reasons over live data and decides what to write. That flexibility is powerful — and it means a small change to the task brief can have large effects.

Governance reduces risk in two ways:
- **At authoring time**: require review before a new or changed workflow can run.
- **At runtime**: use frontmatter controls to limit what the agent can affect.

### Require code review on workflow files

Treat `.github/workflows/*.md` and `.github/workflows/*.lock.yml` the same as production code:

1. Open your repository on GitHub and click **Settings** → **Branches**.
2. Click **Add branch ruleset** and name it `Protect agentic workflows`.
3. Set **Target branches** to `main` (or your default branch).
4. Enable **Require a pull request before merging** and set **Required approvals** to at least **1**.
5. Under **Restrict file paths**, add `.github/workflows/*.md` and `.github/workflows/*.lock.yml`.
6. Click **Create**.

Now any change to a workflow definition or its compiled lock file must pass a pull-request review before it merges.

<details>
<summary>🖥️ What about CODEOWNERS?</summary>

Add a `CODEOWNERS` entry so the right team is always auto-requested as a reviewer:

```
.github/workflows/ @your-org/workflow-reviewers
```

Place this in `.github/CODEOWNERS` and commit it. GitHub will automatically request the `workflow-reviewers` team on any PR that touches workflow files.

</details>

### Declare protected files in frontmatter

The [`protected-files`](https://github.github.com/gh-aw/reference/permissions/#protected-files) field prevents the agent from writing to sensitive paths — even if a malicious prompt injection tries to trick it.

```yaml
---
name: Daily Status Report
on:
  schedule: daily on weekdays
permissions:
  contents: write
  issues: write
safe-outputs:
  - add_comment
protected-files:
  - .github/**
  - CODEOWNERS
  - "*.lock.yml"
---
```

This ensures the agent can write issues and comments but cannot touch workflow definitions, lock files, or access control files.

> [!TIP]
> `protected-files` uses glob patterns. Protect anything a misdirected write could weaponize: CI config, deploy keys, lock files, and ownership declarations.

### Apply a minimum-permissions policy

Define a team convention for the four most common workflow permission patterns:

| Workflow type | Recommended `permissions:` |
|---|---|
| Read-only analysis | `contents: read` |
| Comment-only (issues/PRs) | `contents: read`, `issues: write` |
| Automated PR creation | `contents: write`, `pull-requests: write` |
| Self-updating repo memory | `contents: write` |

Keep `permissions:` narrow. Any scope not listed here defaults to `none`. Review pull requests against this table: a new workflow requesting `contents: write` without a clear write need should be questioned.

### Enforce `max-ai-credits` as a policy default

Ask your GitHub administrator to set an org-level default for `max-daily-ai-credits` in the organization's Copilot settings. For individual workflow files, adopt a team convention:

- Every new workflow file must declare `max-ai-credits` explicitly.
- Every scheduled workflow must also declare `max-daily-ai-credits`.
- The on-call engineer reviews any PR that raises these values above the team baseline.

Document this as a one-page policy checklist and link it from your repository's `CONTRIBUTING.md`.

### Review the governance checklist

Before marking a new agentic workflow as production-ready, verify these items:

1. Branch protection requires at least one reviewer approval for changes to `.github/workflows/`.
2. A `CODEOWNERS` entry routes workflow PRs to a designated team.
3. Every workflow file declares `permissions:` with the minimum scope needed.
4. Every workflow with write access declares `protected-files` covering CI config and ownership files.
5. Every workflow declares `max-ai-credits` and, for scheduled workflows, `max-daily-ai-credits`.
6. `safe-outputs` lists only the write actions the workflow legitimately needs.
7. The lock file (`.lock.yml`) is committed alongside the workflow definition.

> [!NOTE]
> For GHES environments, your site administrator may enforce additional policies through the enterprise policy editor. Review the [enterprise governance guide](https://github.github.com/gh-aw/guides/governance/) for GHES-specific controls.

## ✅ Checkpoint

- [ ] Branch protection requires pull-request review before merging changes to workflow files
- [ ] A `CODEOWNERS` entry assigns a reviewer team to `.github/workflows/`
- [ ] Your workflow frontmatter includes `protected-files` covering at least `.github/**` and `*.lock.yml`
- [ ] `permissions:` in your workflow matches one of the minimum-scope patterns in the table above
- [ ] `max-ai-credits` and `max-daily-ai-credits` are declared in every scheduled workflow
- [ ] You can walk through the seven-item governance checklist above for your workflow

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

# Govern Your Agentic Workflows at Scale

> _Enterprise teams need more than working workflows — they need auditability, policy alignment, and a reproducible review process._

## 🎯 What You'll Do

You'll apply a lightweight governance model to your agentic workflows: document the permissions they require, attach a policy review comment to your workflow file, and validate your setup against the gh-aw governance guide. By the end, you'll have a workflow that is ready to present to a security or compliance reviewer.

## 📋 Before You Start

- You have a scheduled workflow running from [Schedule It to Run Every Day](12-test-and-iterate.md).
- You have reviewed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Recommended: your organisation uses GitHub Enterprise Cloud (GHEC) or GitHub Enterprise Server (GHES) 3.12+.

## Steps

### Understand why governance matters for agentic workflows

Agentic workflows call an AI model and, depending on their brief, can read issues, comment on pull requests, push files, or trigger downstream jobs. Each of those actions requires a **GitHub token permission** and leaves a trail that a security reviewer can audit.

A governance review asks three questions:

1. **What does this workflow touch?** Which GitHub resources does it read or write?
2. **Is least-privilege applied?** Does the `permissions:` block grant only what is needed?
3. **Is the output safe?** Does the workflow use safe outputs (no direct `git push` in the agent step)?

### Audit your workflow's permissions

Open your `daily-status.md` (or equivalent) workflow file. Find the `permissions:` block in the frontmatter.

A minimal read-only status report needs only:

```yaml
permissions:
  contents: read
  issues: read
```

If your workflow also posts a comment on an issue, add:

```yaml
permissions:
  contents: read
  issues: write
```

Remove any permission you are not actively using. Over-broad permissions — especially `contents: write` combined with `issues: write` — significantly widen the blast radius if a prompt-injection attack succeeds.

> [!TIP]
> Not sure which permissions your workflow uses? Read the [Permissions reference](https://github.github.com/gh-aw/reference/permissions/) and cross-check it against every `safe-output` tool call in your workflow brief.

### Add a governance comment block to your workflow

At the top of your workflow `.md` file, just below the frontmatter closing `---`, add a comment block that documents the intent and permissions for any reviewer:

```markdown
<!-- governance
  owner: @your-username
  reviewed: YYYY-MM-DD
  permissions-rationale: >
    contents:read — reads commit history for status report.
    issues:read   — reads open issues for status report.
  safe-outputs-only: true
-->
```

This block is ignored by the compiler and by GitHub Actions. It is purely for human reviewers and automated policy scanners.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/daily-status.md` in your repository on GitHub.
2. Click the **pencil icon (✏️)** to edit the file.
3. Paste the governance comment block below the frontmatter `---`.
4. Click **Commit changes** and commit directly to your default branch.

</details>

### Review the gh-aw governance guide

The [Governance guide](https://github.github.com/gh-aw/guides/governance/) covers:

- How to apply policy-as-code checks to workflow files in CI
- How to enforce an allowed-tools list across an organisation
- How audit logs surface agentic workflow activity in GHEC and GHES

Skim the guide and identify one practice that your team does not yet follow. Note it in a new issue in your practice repository — this becomes your governance backlog.

### Run a self-assessment

Answer the following questions about your workflow and check each box when satisfied:

- [ ] The `permissions:` block grants only what the workflow actually uses.
- [ ] The workflow uses `safe-outputs` for all GitHub writes (no raw `git push` or `gh api` in the agent step).
- [ ] A governance comment block documents the owner and rationale.
- [ ] You have read the [Governance guide](https://github.github.com/gh-aw/guides/governance/) and noted at least one improvement for your team.

> [!NOTE]
> On GHES, your site administrator may enforce additional restrictions on which AI models and tools workflows can call. Check with your admin before deploying workflows that reference external MCP servers or non-default models.

## ✅ Checkpoint

- [ ] Your workflow `permissions:` block uses least-privilege — no unused permissions remain
- [ ] A `<!-- governance ... -->` comment block is present in your workflow file
- [ ] You can explain what `safe-outputs-only: true` signals to a reviewer
- [ ] You opened a new issue in your practice repository with at least one governance improvement for your team
- [ ] You read the [Governance guide](https://github.github.com/gh-aw/guides/governance/) and can name one policy-as-code technique for enforcing workflow standards

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

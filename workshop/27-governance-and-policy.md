<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _A workflow that runs in production is a policy decision — learning to apply protected files, permission boundaries, and org-level controls keeps agentic automation safe as your team grows._

## 🎯 What You'll Do

You'll add `protected-files` to your workflow to prevent the agent from modifying sensitive paths. You'll scope `permissions` to the minimum the task needs. You'll also learn how organization administrators control agentic workflows across a fleet of repositories.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have at least one `.github/workflows/*.md` agentic workflow file in your repository.
- _(Enterprise users)_ You have admin access to your GitHub organization, or you know who to ask.

## Steps

### Lock down sensitive files with `protected-files`

The `protected-files` field tells the gh-aw runtime to refuse any agent write to matching paths — even if the model generates a plausible-sounding reason.

Open your agentic workflow `.md` file and add a `protected-files` block to the frontmatter:

```yaml
---
name: Daily Status Report
on:
  schedule:
    - cron: "0 8 * * 1-5"
permissions:
  contents: read
  issues: write
protected-files:
  - ".github/workflows/**"
  - "CODEOWNERS"
  - "package-lock.json"
  - "*.lock"
---
```

The patterns follow `.gitignore` glob syntax. Any agent attempt to modify a matching file is blocked and logged — the run continues, but the write is rejected.

<details>
<summary>🖥️ GitHub UI alternative</summary>

1. Navigate to `.github/workflows/` and click the workflow file.
2. Click the **pencil icon (✏️)** to edit.
3. Add the `protected-files:` block inside the frontmatter fences.
4. Click **Commit changes**.

</details>

> [!TIP]
> Start with `.github/workflows/**` and `CODEOWNERS` — these are the paths most targeted in prompt-injection attempts.

### Tighten `permissions` to the minimum needed

Review the `permissions:` block in your workflow. Remove any scope your workflow does not actually use.

A read-only status report only needs:

```yaml
permissions:
  contents: read
  issues: write
```

A workflow that creates pull requests needs `pull-requests: write` but still does **not** need `contents: write` if you rely on `safe-outputs` to submit the PR.

Principle: grant `write` only when the workflow genuinely creates or modifies a resource. Use `read` for everything else.

### Understand organization-level controls (enterprise teams)

Organization administrators can control which repositories run agentic workflows and which model is available.

| Setting | Where to find it |
|---------|-----------------|
| Copilot in GitHub Actions | Org → **Settings** → **Copilot** → **Policies** |
| Actions permissions | Org → **Settings** → **Actions** → **General** |
| SAML SSO enforcement | Org → **Settings** → **Authentication security** |

> [!NOTE]
> On GitHub Enterprise Server (GHES), the Copilot engine requires a Copilot Enterprise licence and outbound access to `api.githubcopilot.com`. Confirm both with your site administrator before relying on the Copilot model.

### Review blocked writes in the audit report

After your next workflow run, use the audit command to verify that `protected-files` enforcement appears in the report:

```bash
gh aw audit <run-id>
```

Look for a `protected_file_blocks` section. If any block events appear, the runtime stopped a write that the model attempted. That is the feature working correctly — not an error.

## ✅ Checkpoint

- [ ] You added a `protected-files` block to your workflow frontmatter covering at least `.github/workflows/**`
- [ ] You reviewed your `permissions:` block and removed any scope your workflow does not use
- [ ] You can explain, in one sentence, why `contents: read` is safer than `contents: write` for a reporting workflow
- [ ] _(Enterprise users)_ You confirmed the Copilot in GitHub Actions policy is enabled for your org
- [ ] _(Enterprise users)_ You know who to contact if SAML SSO enforcement affects your workflow token

<!-- journey: all -->
**Next:** Return to [What's Next? Keep Exploring](14-next-steps.md) to choose your next advanced topic.
<!-- /journey -->

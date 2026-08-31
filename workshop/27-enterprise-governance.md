<!-- page-journey: all -->

# Govern Agentic Workflows at Enterprise Scale

> _A workflow that runs fine in one repository needs a governance plan before it runs across hundreds._

## 🎯 What You'll Do

You'll apply a lightweight governance model to your agentic workflows: define who can author and publish them, lock down permissions at the organization level, and export audit logs. By the end you have a repeatable checklist you can hand to a platform team.

## 📋 Before You Start

- You've completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin or org-owner access (or a willing collaborator who does) for some steps below.
- GHES users: confirm your instance version supports `gh-aw`. Ask your admin if unsure.

## Steps

### Define who can publish workflows

Agentic workflows carry real write authority. Treat them like production code.

| Tier | Who | What they can do |
|------|-----|-----------------|
| **Authors** | Any developer | Draft `.md` files in feature branches |
| **Reviewers** | Platform team | Approve PRs that change `permissions:` or `safe-outputs:` |
| **Publishers** | Platform team | Merge to default branch — only merged workflows run |

You need no extra tooling: a branch protection rule plus `CODEOWNERS` is enough.

### Protect the workflows directory with CODEOWNERS

1. Create or edit `.github/CODEOWNERS` in your repository.
2. Add one line:

```text
.github/workflows/ @your-org/platform-team
```

Commit the file. Then go to **Settings → Branches**, edit your default-branch rule, and enable **Require a pull request** with **Required approvals: 1**.

> [!NOTE]
> On GitHub Enterprise Server, `CODEOWNERS` and branch protection work identically to github.com — no extra configuration is needed.

### Set the organization-level default permission

1. Go to **Settings → Actions → General** (organization settings, not repository settings).
2. Under **Workflow permissions**, choose **Read repository contents and packages permissions**.

Any workflow that needs `contents: write` or `issues: write` must now declare it explicitly — nothing runs with broad write access by accident.

> [!TIP]
> On GHEC, enforce this across all repositories from **Enterprise settings → Policies → Actions**.

### Adopt a naming convention

When dozens of workflows run across many repositories, a consistent `name:` field makes filtering easy:

```text
[team-name] purpose   e.g.  [platform] daily-status
```

Use this prefix in every workflow's frontmatter `name:` field. It surfaces cleanly in the Actions tab and in audit log exports.

### Export the audit log

To review which workflows ran and who triggered them:

```bash
gh api "orgs/{org}/audit-log?phrase=action:workflows&per_page=100" \
  --paginate \
  --jq '.[] | {action, actor, repo, created_at}' \
  > audit-export.json
```

Look for `workflows.completed` events from unexpected actors or repositories.

> [!NOTE]
> On GHES, the audit log endpoint path differs. Your admin can confirm the correct path and whether streaming to a SIEM is configured.

<details>
<summary>🖥️ GitHub UI alternative — browse the audit log</summary>

1. Go to your organization's **Settings → Audit log**.
2. Filter by **Action → workflows** and set a date range.
3. Click **Export** to download a CSV for offline review.

</details>

## ✅ Checkpoint

- [ ] `.github/CODEOWNERS` routes `.github/workflows/` changes to a reviewer group
- [ ] Branch protection on your default branch requires at least one PR approval
- [ ] The organization-level default workflow permission is set to **read-only**
- [ ] Your workflow `name:` follows the `[team-name] purpose` convention
- [ ] You exported the audit log and identified at least one `workflows.completed` event
- [ ] You can explain the three-tier author / reviewer / publisher model to a colleague

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

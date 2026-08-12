<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows with Org-Level Policy

> _Reliable automation in an enterprise requires more than a running workflow — it requires clear policies that keep AI-driven actions auditable, safe, and aligned with your organisation's standards._

## 🎯 What You'll Do

You'll define a lightweight governance policy for your agentic workflow, configure organisation-level controls in GitHub, and produce a policy document your team can use when reviewing or approving new agentic workflows.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have `admin` or `owner` access to the GitHub organisation where your workflow runs — or you can work alongside someone who does.
- _(GHES users)_ Confirm with your GitHub Enterprise administrator that Actions policies are accessible at the organisation level on your instance.

## Steps

### Understand what governance covers

Governance for agentic workflows means answering three questions for every workflow your team ships:

1. **What can the workflow read and write?** (permissions and safe-output declarations)
2. **Who approved it, and is there a record?** (code review, branch protection, audit log)
3. **What happens when it misbehaves?** (timeouts, rate limits, incident runbook)

### Review the required-reviewers protection

The most direct governance control is requiring a code review on every change to workflow files.

1. In your repository, go to **Settings** → **Branches**.
2. Click **Add branch ruleset** (or edit your existing `main` protection rule).
3. Under **Require a pull request before merging**, check **Require approvals** and set the minimum to **1**.
4. Under **Restrict who can push**, add only the people or teams who are permitted to ship new workflows.
5. Click **Save changes**.

> [!TIP]
> If your organisation uses a monorepo, consider limiting the protection to the `.github/workflows/` path using a **push ruleset** with file path restrictions instead of a branch-wide rule.

### Document your workflow's trust boundary

Open your workflow's `.md` file and add a comment block near the top recording four key facts any reviewer needs:

```markdown
<!--
governance:
  owner: "@your-team-or-username"
  approved-by: "@reviewer"
  last-reviewed: "YYYY-MM-DD"
  trust-boundary: |
    Reads: repository commits and open issues (GITHUB_TOKEN, read-only)
    Writes: creates one issue comment per run via safe-outputs
    Network: github.com only (network.allowed-domains restriction)
    AI model: copilot (GitHub-managed, no external key)
-->
```

This comment is not parsed by `gh aw compile` — it is for human reviewers only. Update it whenever permissions or safe-output declarations change.

### Set an org-level Actions policy for agentic workflows

1. Go to your organisation's **Settings** → **Actions** → **General**.
2. Under **Workflow permissions**, confirm **Read repository contents and packages permissions** is selected as the default. Workflows that need write access must opt in via frontmatter — this default ensures new workflows start with least privilege.
3. Under **Allow GitHub Actions to create and approve pull requests**, decide whether your governance policy permits this. For most enterprise teams the safest starting point is **disabled**.
4. Click **Save**.

> [!NOTE]
> On GitHub Enterprise Server, these settings may be managed at the enterprise level by an administrator. If the organisation-level controls are greyed out, ask your GHES admin to review the enterprise policy.

## ✅ Checkpoint

- [ ] Your repository's `main` branch requires at least one approving review before a workflow file can be merged
- [ ] You added a `governance:` comment block to your workflow's `.md` file recording owner, reviewer, review date, and trust boundary
- [ ] Your organisation's default workflow permission is set to **read-only**
- [ ] You can describe — in one or two sentences — the trust boundary your workflow operates within
- [ ] You know who in your organisation to contact if the workflow begins behaving unexpectedly

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

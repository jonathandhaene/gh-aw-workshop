<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows at Scale

> _Shipping one workflow is a proof of concept — shipping ten with consistent quality and controlled permissions is engineering._

## 🎯 What You'll Do

Set up organisation-level policy controls for agentic workflows. You will enable or restrict which repositories can run agentic workflows, review the Copilot model-access settings your administrator controls, and apply a simple approval gate so that changes to a production workflow require a peer review before they reach the default branch.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) or [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to the GitHub organisation, **or** you know who does — some steps in this node require organisation-level settings.

> [!NOTE]
> If you are on `github.com` without an organisation, you can still follow the repository-level steps. Skip the sections that reference the organisation settings page.

## Understand the two layers of governance

Governance for agentic workflows works at two levels:

1. **Organisation policy** — your GitHub admin controls whether agentic workflows can run at all and which models are available.
2. **Repository branch protection** — you control who can push changes to the workflow file and whether a pull-request review is required before a change reaches production.

Both layers matter. Organisation policy sets the guardrails; branch protection enforces your team's change-control process.

## Review organisation-level Copilot settings

Your organisation admin can:

- Enable or disable the Copilot cloud agent entirely for the organisation.
- Restrict which models are available (for example, allow only `gpt-4o` and block experimental models).
- Require Copilot Enterprise licensing for any user who triggers an agentic workflow.

To review these settings (admin required):

1. Go to your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Policies**.
3. Confirm that **Copilot in GitHub Actions** is enabled for the repositories that need it.
4. Review the **Available models** list and note any that are blocked.

![Copilot policies page in organisation settings](images/27-org-copilot-policies.png)

> [!TIP]
> If a colleague's workflow fails with a model-not-available error, the most common cause is an organisation policy. Point the admin to the **Policies** page before spending time debugging the workflow file.

## Protect your workflow file with branch rules

A scheduled agentic workflow file lives in `.github/workflows/`. On a busy team, unreviewed changes to that file can cause unexpected runs, runaway costs, or a broken schedule.

Add a branch ruleset to require a pull-request review for changes to workflow files:

1. Go to your repository on GitHub.
2. Click **Settings** → **Rules** → **Rulesets** → **New ruleset** → **New branch ruleset**.
3. Name the ruleset `Protect workflow files`.
4. Set **Target branches** to `Default branch`.
5. Under **Rules**, enable **Require a pull request before merging**.
6. Under **Additional settings**, enable **Restrict file paths** and add the pattern `.github/workflows/**.md` and `.github/workflows/**.lock.yml`.
7. Click **Create**.

Now any change to an agentic workflow file requires a pull-request review before it merges. The CI run that compiles the workflow (`gh aw compile`) can act as a status check inside that PR.

<details>
<summary>🖥️ Alternative: protect with a CODEOWNERS file</summary>

Add a `CODEOWNERS` file at `.github/CODEOWNERS`:

```
.github/workflows/ @your-org/workflow-maintainers
```

This assigns ownership of all workflow files to a team. GitHub automatically requests a review from that team when a PR touches those files, without requiring a full branch ruleset.

</details>

## Add a required status check for compilation

Enforce that every workflow PR compiles cleanly before it can merge:

1. In your repository, go to **Settings** → **Rules** → **Rulesets** → open the ruleset you just created.
2. Enable **Require status checks to pass**.
3. Add the check name that your CI job reports (for example, `compile-workflow` or whatever your Actions job is named).
4. Click **Save changes**.

Now a PR that introduces invalid YAML in the frontmatter, or a broken Markdown brief, cannot merge until the compile check passes.

## Review your workflow's permissions scope

Open your workflow file (for example `daily-status.md`) and confirm that the `permissions:` block grants only what the workflow actually needs:

```yaml
permissions:
  issues: write
  contents: read
```

The principle of least privilege applies to agentic workflows just as it does to any other code. If your workflow only writes issues and reads files, it should not have `pull-requests: write` or `actions: write`.

> [!NOTE]
> On GitHub Enterprise Server, repository admins can enforce a maximum-permissions policy that caps what any workflow can request. If your permission block is being silently downgraded, ask your GHES admin whether a cap policy is active.

## ✅ Checkpoint

- [ ] You reviewed the **Copilot Policies** page in your organisation (or confirmed with your admin that agentic workflows are enabled)
- [ ] You know which models are available and which are blocked in your organisation
- [ ] You created a branch ruleset that requires a pull-request review for changes to `.github/workflows/`
- [ ] You understand how to add a required compile status check to the ruleset
- [ ] Your workflow's `permissions:` block grants only the permissions the workflow actually uses
- [ ] You can explain the difference between organisation-level policy and repository branch protection

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

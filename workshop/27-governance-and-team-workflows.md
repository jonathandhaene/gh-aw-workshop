# Govern and Scale Agentic Workflows Across Your Organisation

> _One workflow running in one repo is a proof of concept — a shared governance model turns it into a platform your whole team can trust._

## 🎯 What You'll Do

You'll learn how to centralise agentic workflow definitions so multiple repositories can reuse them without drifting apart. You'll also configure the permissions and policies that enterprise teams need before rolling out workflows at scale.

## 📋 Before You Start

- You have completed [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md) or [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to at least one GitHub organisation (or a test org you control).

## Steps

### Understand the two distribution models

You can distribute agentic workflows in two ways:

| Model | How it works | Best for |
|-------|-------------|----------|
| **Centralised catalog** | One source repo holds `.md` definitions; teams install with `gh aw add` | Organisation-wide standards, tightly controlled updates |
| **Template repository** | A GitHub template repo pre-populates `.github/workflows/` for new repos | Greenfield projects, onboarding new teams quickly |

Choose the model that matches how your organisation already manages shared Actions — consistency reduces cognitive load for maintainers.

### Create a centralised workflow catalog repo

Create a repository called something like `agentic-workflow-catalog` in your organisation.

**Using the GitHub UI (recommended):**

1. Go to **github.com/organizations/\<org\>/repositories/new** (or your GHES equivalent).
2. Name the repository `agentic-workflow-catalog`.
3. Set visibility to **Internal** (recommended for GHEC/GHES) or **Private**.
4. Add a `README.md` describing the catalog purpose and usage.
5. Click **Create repository**.

<details>
<summary>🖥️ Alternative: create via terminal</summary>

```bash
gh repo create <org>/agentic-workflow-catalog --internal --description "Shared agentic workflow catalog"
```

</details>

Add your `.md` workflow definitions and their compiled `.lock.yml` files to the repo. Commit via the UI pencil icon (✏️) or your local editor.

### Publish a workflow to the catalog

In your catalog repo, add a brief install comment near the top of each workflow `.md` file so that teammates know what to customise:

```markdown
<!--
Install this workflow in any repository:
  gh aw add <org>/agentic-workflow-catalog/daily-status

Customise:
  - `on.schedule`: adjust the cron expression for your time zone
  - `max-ai-credits`: set a budget appropriate for your repo size
-->
```

> [!TIP]
> Keep install instructions short and actionable. Teammates reaching for `gh aw add` are in a terminal — they don't need a long README in the file itself.

### Set organisation-level permissions

Before workflows run across multiple repositories, confirm the Actions permissions your organisation enforces.

1. Go to **Organisation Settings → Actions → General**.
2. Under **Actions permissions**, choose **Allow all actions and reusable workflows** or scope it to your catalog repo.
3. Under **Workflow permissions**, confirm **Read and write permissions** is enabled if your workflows post comments or create issues (enterprise admins often set this to read-only by default).

> [!NOTE]
> On GitHub Enterprise Server, these settings may be managed at the enterprise level and overridden by a site admin. Check with your GitHub administrator if you cannot change them at the organisation level.

### Pin workflow versions for stability

When teams install from your catalog, they get the current `HEAD` by default. For production workflows, pin to a release tag:

```bash
gh aw add <org>/agentic-workflow-catalog/daily-status@v1.2.0
```

Use GitHub Releases on your catalog repo to publish stable versions. Tag releases semantically (`v1.0.0`, `v1.1.0`) so teams can audit which version they are running.

### Add a policy comment to your organisation README

If your organisation has a `.github` repository with a profile `README.md`, add a short section pointing teams to the catalog. This is the single source of truth for "how do I get a workflow here?"

```markdown
## Agentic Workflows

Our shared workflow catalog lives at [<org>/agentic-workflow-catalog](<url>).
Install a workflow with `gh aw add <org>/agentic-workflow-catalog/<workflow-name>`.
Contact @platform-team to request a new workflow or report a problem.
```

## ✅ Checkpoint

- [ ] You created (or identified) a centralised workflow catalog repository in your organisation
- [ ] You chose either the centralised catalog model or template repository model, and can explain why
- [ ] Your catalog repo contains at least one workflow `.md` and its compiled `.lock.yml`
- [ ] You verified the organisation-level Actions permissions allow your workflows to run
- [ ] You can construct the `gh aw add` install command for a workflow in your catalog
- [ ] You know where to find (or who to ask about) the enterprise-level permission overrides on your instance
- [ ] You added a policy pointer in your organisation's profile README or equivalent documentation

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

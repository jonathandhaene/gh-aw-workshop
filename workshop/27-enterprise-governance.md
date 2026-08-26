<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Bringing agentic workflows to a team is easy — keeping them safe, approved, and auditable at scale is an engineering discipline._

## 🎯 What You'll Do

You will review the controls available to GitHub Enterprise administrators for governing agentic workflows across an organisation: required approvals, allowed-engines policies, network restrictions, and the workflow catalog. By the end you will know how to set up a lightweight governance model that lets teams move fast without creating uncontrolled AI automation.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You have admin access to a GitHub organisation, or you are following along as a contributor who wants to understand the model.
- _(GHES users)_ Your instance is version 3.12 or later. Features marked GHEC may not yet be available on GHES — check your site administrator.

## Steps

### Understand the governance layers

Governance for agentic workflows operates at three levels, from broadest to narrowest:

| Level | Who controls it | What it governs |
|-------|----------------|-----------------|
| **Organisation policy** | Org admin | Which engines are allowed; whether network egress is permitted |
| **Repository settings** | Repo admin | Which workflows are enabled; required reviewers before first run |
| **Workflow frontmatter** | Workflow author | Minimal `permissions:`, `safe-outputs`, and `network.allowed-domains` |

Think of these as concentric rings. Organisation policy sets the outer boundary; workflow frontmatter provides the innermost constraint. A workflow can only do what all three layers permit.

### Set an allowed-engines policy

Restrict which AI models your organisation's workflows can use.

1. Open your organisation on GitHub.
2. Click **Settings** → **Copilot** → **Agentic Workflows**.
3. Under **Allowed engines**, choose one of:
   - **Any engine** — no restriction (default, not recommended for production orgs).
   - **GitHub-hosted models only** — restricts to Copilot-managed endpoints; prevents keys for third-party APIs.
   - **Specific engines** — allowlist individual engine identifiers such as `copilot`.

> [!NOTE]
> If a workflow's frontmatter specifies an engine that the org policy disallows, the run fails at the compilation gate with a clear error. Authors see this immediately during `gh aw compile`.

### Require a review before a new workflow's first run

Every new agentic workflow in a repository can be held for human review before its first production run.

1. In **Settings** → **Copilot** → **Agentic Workflows**, enable **Require review for new workflows**.
2. Add at least one required reviewer (a team or individual).
3. When a contributor opens a pull request that adds a new `.md` workflow file, the review gate activates automatically.
4. The reviewer checks:
   - `permissions:` is minimal — no `contents: write` unless the workflow genuinely writes files.
   - `safe-outputs` lists the intended write actions and nothing else.
   - `network.allowed-domains` is either absent (no egress) or lists only known, trusted endpoints.

<details>
<summary>🖥️ What reviewers look for — a quick checklist</summary>

- [ ] `permissions:` contains only the scopes the brief actually needs.
- [ ] Every `safe-outputs` entry has a clear justification in the brief.
- [ ] `network.allowed-domains` is absent or tightly scoped (no wildcard `*.example.com` entries).
- [ ] The agent brief does not instruct the agent to read or write secrets.
- [ ] Schedule is set to a reasonable frequency; not `schedule: every 5 minutes`.

</details>

### Control network egress with allowed-domains

Agentic workflows can fetch external data using `network.allowed-domains` in their frontmatter. Organisation policy can restrict this.

1. In **Settings** → **Copilot** → **Agentic Workflows**, find **Network egress**.
2. Choose:
   - **Blocked** — no workflow in the org can make external requests.
   - **Org allowlist only** — workflows must list domains already in the org-level allowlist; individual `network.allowed-domains` entries are ignored unless they appear in the list.
   - **Workflow-controlled** — each workflow's frontmatter governs its own egress (default).

> [!TIP]
> Starting with **Org allowlist only** and then adding domains on request is a low-friction governance model: teams are unblocked quickly and the org maintains a visible record of approved external dependencies.

### Publish approved workflows to the workflow catalog

The [workflow catalog](https://github.github.com/gh-aw/reference/releases/) lets teams install pre-approved workflows with one command. Publishing to your organisation's internal catalog is the standard way to distribute governed patterns.

1. Tag your workflow repository with the `gh-aw-catalog` topic.
2. Ensure the workflow's `README.md` has a `## Install` section with the `gh aw install` command (filled in by `gh aw publish`).
3. Run `gh aw publish` from your repository root.
4. Org members can now install your workflow with `gh aw install <your-org>/<workflow-name>`.

### Review the governance audit trail

Every agentic workflow run produces a signed audit record. Organisation admins can query these records across all repositories.

1. Open your organisation → **Insights** → **Copilot** → **Agentic Workflows**.
2. Filter by date range, repository, or engine.
3. Export to CSV for compliance reporting.

> [!TIP]
> Pair this with the [Audit Log Streaming](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise) feature (GHEC/GHES) to send workflow governance events to your SIEM.

## ✅ Checkpoint

- [ ] You can describe the three governance layers (org policy, repo settings, workflow frontmatter) and what each one controls
- [ ] You know how to restrict allowed engines for your organisation
- [ ] You reviewed a workflow's frontmatter against the minimal-permissions checklist
- [ ] You understand how `network.allowed-domains` interacts with org-level network egress policy
- [ ] You know how to publish an approved workflow to the internal catalog

**Next:** [What's Next? Keep Exploring](14-next-steps.md)

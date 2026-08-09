<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Enforce Organisation Policies for Agentic Workflows

> _A workflow that works in your personal repo may behave differently when 50 teams run it — organisation-level policies are your first line of defence against drift, overspend, and unexpected writes._

## 🎯 What You'll Do

You'll review the key policy controls available to GitHub organisation administrators for agentic workflows: required permission scopes, network allow-lists, and spending caps. You'll also add a policy comment block to your own workflow file so future reviewers can see the intent and constraints at a glance.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or [Audit and Monitor Your Agentic Workflows](25-audit-and-observability.md).
- You have read access to your GitHub organisation settings, or you are working in your personal repository.
- You know what `permissions:` and `network:` blocks do — if not, skim [Permission Escalation in Agentic Workflows](side-quest-17-04-permission-escalation.md) first.

---

## Understand what org admins control

Organisation administrators can set policies that apply to every agentic workflow they own. Knowing these controls helps you write workflows that pass checks without surprises.

| Policy area | What admins enforce | What you must do |
|---|---|---|
| **AI Credit limits** | Daily and per-run AIC caps | Declare `max-ai-credits` in your frontmatter |
| **Network egress** | Global domain allow-list | List every external hostname in `network.allowed-domains` |
| **Copilot access** | Which repos may trigger the Copilot cloud agent | Confirm your repo is in scope |
| **Actions permissions** | Default token permission level | Always declare `permissions:` explicitly — never rely on the default |

> [!NOTE]
> On GHES, policy enforcement applies to all repos enterprise-wide by default. On GHEC, policies are per-organisation unless the enterprise sets top-level enforcement. Ask your administrator which applies.

---

## Add a policy block to your workflow

A policy comment block documents intent for reviewers. Open `daily-status.md` and add this block immediately after the closing `---` of the frontmatter:

```markdown
<!--
policy:
  author: <your-github-handle>
  reviewed: <YYYY-MM-DD>
  data-sensitivity: internal          # internal | public | confidential
  egress: none                        # none | github-api | external
  write-scope: issues                 # list the safe-outputs types this workflow uses
  max-ai-credits: 200                 # per-run cap (must match frontmatter value)
  max-daily-ai-credits: 500           # daily cap (must match frontmatter value)
-->
```

Fill in the fields for your workflow. Agree on a standard template with your team and reference it in a `CONTRIBUTING.md` or a workflow authoring guide.

<details>
<summary>🖥️ GitHub UI path</summary>

1. Navigate to your repository on GitHub.
2. Open `daily-status.md` and click the **pencil icon (✏️)** to edit.
3. Paste the policy block immediately after the closing `---` of the frontmatter.
4. Click **Commit changes**, add a message like `docs: add policy block to daily-status`, and commit.

</details>

---

## Apply least-privilege permissions

Make sure your workflow's `permissions:` block grants only what each safe output needs. For a workflow that posts issue comments, the correct scope is:

```yaml
permissions:
  issues: write
  contents: read
```

Avoid `contents: write` unless your workflow must push files. A workflow with write access that processes untrusted text (issue bodies, PR titles) can be steered into committing unexpected changes if its brief is not tightly scoped.

---

## Lock down egress

If your workflow only calls GitHub APIs, restrict network egress:

```yaml
network:
  allowed-domains:
    - api.github.com
    - github.com
```

If your workflow calls an external MCP server or REST API, list only the specific hostname — no wildcards. This stops a misdirected agent from sending data to an unexpected endpoint.

> [!TIP]
> You can verify the domains your workflow contacts by reading the `firewall.md` artifact in the workflow run. See [Audit Reference](side-quest-25-01-audit-reference.md) for field details.

---

## Before you share: a governance checklist

Run through this before opening a PR to add your workflow to a shared repository:

- Explicit `permissions:` block — no `*` wildcards, no `write-all`
- `network.allowed-domains` — every external hostname listed, no wildcards
- `max-ai-credits` and `max-daily-ai-credits` in frontmatter
- `timeout-minutes` set on the job
- Policy comment block filled in and dated
- At least one team member has reviewed the task brief for prompt-injection risk

---

## ✅ Checkpoint

- [ ] You can name at least three policy areas that organisation administrators control for agentic workflows
- [ ] Your `daily-status.md` workflow file has an explicit `permissions:` block with no `write-all` or wildcard scopes
- [ ] Your `daily-status.md` workflow file has a `network.allowed-domains` list (even if it is only `api.github.com`)
- [ ] You added a policy comment block to your workflow file and filled in all fields
- [ ] You know where to find the `firewall.md` egress artifact for a completed workflow run
- [ ] You can explain to a teammate why `contents: write` is riskier than `issues: write` in a workflow that reads user-controlled text

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

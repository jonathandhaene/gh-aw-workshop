<!-- page-journey: all -->
<!-- page-adventure: enterprise -->
# Govern Who Can Run Agentic Workflows

> _As agentic workflows become part of your team's tooling, you need clear policies — not just good intentions._

## 🎯 What You'll Do

Learn how to apply organisation-level controls to agentic workflows: restricting which repositories can run them, limiting approved engines and MCP servers, and documenting a repeatable approval process your team can follow. By the end you will have a lightweight governance template committed to your repository.

## 📋 Before You Start

- You have at least one working agentic workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- Your workflow uses `permissions:` and `safe-outputs:` as introduced in earlier steps.

## Steps

### Understand the governance surface

Three frontmatter fields let you assert policy directly in the workflow file:

| Field | What it controls |
|-------|-----------------|
| `permissions:` | Which GitHub API scopes the agent can exercise |
| `safe-outputs:` | Which write actions the agent is allowed to take |
| `network.allowed-domains:` | Which external domains the agent can reach |

Restricting these three fields is the highest-leverage policy control available per workflow. An organisation-level policy document then specifies which values are acceptable across _all_ workflows.

### Draft a governance policy document

Create a file called `AGENTIC-POLICY.md` in the root of your practice repository. It should answer three questions for every agentic workflow your team runs:

1. **Who approved it?** (name or team, date)
2. **What can it write?** (list the `safe-outputs:` values in use)
3. **What external services can it reach?** (list `network.allowed-domains:` entries, or "none")

A minimal template:

```markdown
# Agentic Workflow Policy

| Workflow | Approved by | Safe outputs | External domains |
|----------|-------------|--------------|------------------|
| daily-report-status | @your-handle (YYYY-MM-DD) | create-issue | none |
| pr-reviewer | @your-handle (YYYY-MM-DD) | add-pr-comment | none |
```

You can edit this file in the GitHub UI:

1. In your repository, click **Add file** → **Create new file**.
2. Name the file `AGENTIC-POLICY.md`.
3. Paste the template above, fill in your workflow rows, and click **Commit new file**.

<details>
<summary>🖥️ Terminal alternative</summary>

```bash
cat > AGENTIC-POLICY.md << 'EOF'
# Agentic Workflow Policy

| Workflow | Approved by | Safe outputs | External domains |
|----------|-------------|--------------|------------------|
| daily-report-status | @your-handle (YYYY-MM-DD) | create-issue | none |
EOF
git add AGENTIC-POLICY.md
git commit -m "docs: add agentic workflow governance policy"
git push
```

</details>

### Apply allowed-domains to existing workflows

If any of your workflows do not already have a `network:` block, add one now. For workflows that only read from GitHub, the correct value is an empty list:

```yaml
network:
  allowed-domains: []
```

An empty list tells gh-aw that the agent must not open any external connections. If your workflow connects to an MCP server or external API, list only those specific domains — no wildcards.

> [!NOTE]
> On GitHub Enterprise Server, your administrator may enforce an organisation-level `allowed-domains` allowlist. Check with your admin before adding external domains.

### Establish a change-review step

Policy only works if someone reviews it. Add a branch protection rule so that changes to `.github/workflows/*.md` and `.github/workflows/*.lock.yml` require at least one approval before merging:

1. Go to **Settings** → **Branches** → **Add branch ruleset**.
2. Enable **Require a pull request before merging** with **Required approvals: 1**.
3. Leave the **Bypass** list empty — the rule applies to everyone.

> [!TIP]
> Consider a CODEOWNERS rule that routes `.github/workflows/*.md` changes to a dedicated workflow-review team.

## ✅ Checkpoint

- [ ] `AGENTIC-POLICY.md` is committed to your repository with at least one workflow row filled in
- [ ] Every workflow you own has an explicit `network.allowed-domains:` block (even if it is an empty list)
- [ ] You can name the three frontmatter fields that represent the governance surface of an agentic workflow
- [ ] Your repository has a branch ruleset that requires approval for changes to workflow files
- [ ] You know how to add a CODEOWNERS rule to route workflow reviews to the right team

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

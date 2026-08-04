<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _Deploying workflows to teams means setting guardrails — learn how to apply organisation-level policies so agentic workflows run safely across every repository._

## 🎯 What You'll Do

You'll set the three key governance controls for agentic workflows at the organisation level: a permissions policy, an approved model list, and a required workflow that validates workflow files on every pull request.

## 📋 Before You Start

- You have completed [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md).
- You are an **organisation owner** or are working with one — several steps require org-admin access.
- _(GHES users)_ Confirm your GHES version is 3.12 or later before continuing.

## Steps

### Set the org-wide permissions policy

Every agentic workflow declares token scopes in frontmatter under `permissions:`. GitHub Actions enforces those scopes at runtime.

1. Go to your organisation's **Settings** → **Actions** → **General**.
2. Under **Workflow permissions**, select **Read repository contents and packages permissions**.
3. Click **Save**.

This makes every workflow start from a read-only baseline. Workflows that need write access must declare it explicitly — a natural forcing function for auditability.

> [!NOTE]
> The Copilot Cloud Agent inherits the permissions block from the workflow's frontmatter. Restricting the org default to read-only applies equally to agent-driven write operations.

### Restrict which models workflows can use

On GitHub Enterprise Cloud and GHES 3.15+, you can set an approved model list.

1. Go to **Settings** → **Copilot** → **Policies**.
2. Find the **Approved models** section (label may vary by GHES version).
3. Add the models your org permits (for example, `github/copilot`).

> [!TIP]
> An approved model list is optional on `github.com` but strongly recommended for enterprise orgs — it prevents unintended drift when GitHub introduces new models.

### Add a required workflow to validate workflow files

A [required workflow](https://docs.github.com/en/actions/using-workflows/required-workflows) runs on every repository in your org when a pull request targets the default branch. Use it to enforce that agentic workflow `.md` files compile cleanly before merging.

Create this file in a policy source repository (such as `.github` or `org-policies`):

```yaml
name: Validate agentic workflows
on:
  pull_request:
    paths:
      - ".github/workflows/*.md"

permissions:
  contents: read

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install gh-aw extension
        run: gh extension install github/gh-aw
        env:
          GH_TOKEN: ${{ github.token }}
      - name: Validate workflow files
        run: |
          for f in .github/workflows/*.md; do
            echo "Validating $f"
            gh aw compile "$f" --validate
          done
        env:
          GH_TOKEN: ${{ github.token }}
```

<details>
<summary>🖥️ GitHub UI: create this file without a local terminal</summary>

1. Open your policy source repository on GitHub.
2. Click **Add file** → **Create new file**.
3. Set the path to `.github/workflows/validate-agentic-workflows.yml`.
4. Paste the YAML above and click **Commit new file**.

</details>

Then register it as a required workflow:

1. Go to your organisation's **Settings** → **Actions** → **Required workflows**.
2. Click **Add required workflow**, select the policy source repo and the file, then **Save**.

Every repository in the org now runs this check on pull requests that touch workflow files.

## ✅ Checkpoint

- [ ] Your organisation's default workflow permissions are set to **read-only**
- [ ] You located the **Approved models** policy page in your org's Copilot settings
- [ ] You created a `validate-agentic-workflows.yml` file in a policy source repository
- [ ] You registered it as a required workflow for your organisation
- [ ] You can explain the three governance levers (permissions, model policy, required workflows) in your own words

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

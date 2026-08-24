# Govern Your Agentic Workflows at Scale

> _Governance turns a personal automation into something your whole organisation can trust._

## 🎯 What You'll Do

Apply a lightweight governance layer to your agentic workflows: restrict which branches can deploy them, require pull-request review before a change goes live, and document ownership so your team knows who to contact when something looks wrong.

By the end of this step you will have a branch-protection rule that gates changes to your workflow files, a `CODEOWNERS` entry that routes reviews to the right people, and a brief policy comment inside your workflow file.

## 📋 Before You Start

- You have a working, scheduled workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) (or any earlier workflow step).
- You have at least `Maintainer` access to the repository, or can ask a repository admin to apply the branch-protection settings for you.

## Steps

### Understand why governance matters for agentic workflows

A standard GitHub Actions workflow does exactly what its YAML says.
An agentic workflow does what an AI model decides — guided by your task brief and frontmatter.
That extra degree of freedom makes change control more important, not less.

Two controls give you most of the protection you need:

1. **Branch protection** — prevent direct pushes to `main` so every workflow change goes through a pull request.
2. **CODEOWNERS** — automatically request review from the person (or team) responsible for agentic workflows whenever those files change.

### Add a `CODEOWNERS` entry

`CODEOWNERS` lives in the root of your repository (or in `.github/` or `docs/`).
Add a line that routes any change inside `.github/workflows/` to yourself or a team:

```text
# Agentic workflow files require review from the workflow maintainer.
.github/workflows/  @your-github-username
```

If you want to route to a team instead, use `@your-org/workflow-reviewers`.

> [!TIP]
> You can scope `CODEOWNERS` to only the `.md` and `.lock.yml` workflow files by using:
> `.github/workflows/*.md  @your-github-username`

<details>
<summary>🖥️ GitHub UI steps</summary>

1. In your repository, click **Add file → Create new file** (or navigate to an existing `CODEOWNERS` file and click the pencil icon ✏️).
2. Set the file path to `.github/CODEOWNERS` if it does not already exist.
3. Add the line shown above, replacing `@your-github-username` with your handle.
4. Click **Commit changes** and choose **Create a new branch** so the change goes through a PR.

</details>

### Protect the default branch

Branch-protection rules ensure every change to your workflow files is reviewed before it reaches `main`.

1. Go to your repository → **Settings** → **Branches**.
2. Under **Branch protection rules**, click **Add rule**.
3. Set **Branch name pattern** to `main`.
4. Enable **Require a pull request before merging**.
5. Enable **Require approvals** and set the count to **1**.
6. Enable **Require review from Code Owners** (this activates your `CODEOWNERS` entry).
7. Click **Save changes**.

> [!NOTE]
> On GitHub Enterprise Server, branch-protection settings are in the same location but may show additional options configured by your organisation's administrator — such as required status checks for enterprise compliance scanners.

### Add a policy comment to your workflow file

A short comment at the top of your workflow `.md` file tells any future editor who owns the workflow and what the approval process is.

Open your `daily-status.md` (or any other workflow file) and add a comment block directly below the closing `---` of the frontmatter:

```markdown
<!--
Workflow owner: @your-github-username
Review policy:  All changes require a pull request reviewed by the workflow owner.
Last reviewed:  YYYY-MM-DD
-->
```

This comment is stripped out by `gh aw compile` and never reaches the compiled `.lock.yml`, so it has no effect on execution.

### Compile and push via pull request

After updating `CODEOWNERS` and your workflow file, run compile to keep the lock file current:

```bash
gh aw compile
```

Then open a pull request from your working branch.
Because you just enabled branch protection, you cannot push directly to `main` — the PR is now required.

> [!TIP]
> UI-first learners can skip the local compile step. Push your edits, open a PR, and let the Actions `compile-on-push` step (if configured) regenerate the lock file — or accept that the lock file updates on the next manual compile.

## ✅ Checkpoint

- [ ] A `CODEOWNERS` file exists in your repository and routes `.github/workflows/` changes to you or your team
- [ ] Your default branch has a protection rule that requires at least one pull-request approval
- [ ] **Require review from Code Owners** is enabled on the branch-protection rule
- [ ] Your workflow `.md` file includes a policy comment with an owner handle and review policy
- [ ] You opened (and, optionally, merged) a pull request to apply those changes — confirming the protection rule is active
- [ ] You can explain to a teammate why CODEOWNERS and branch protection together reduce the risk of an unreviewed change shipping to production

<!-- journey: all -->
Want to explore more enterprise-focused topics? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->

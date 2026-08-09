<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Your Agentic Workflows with Organisation Policies

> _Agentic workflows take real actions on your behalf — good governance ensures only the right workflows, approved by the right people, can run in production._

## 🎯 What You'll Do

You'll apply three complementary governance controls to your agentic workflow: a `CODEOWNERS` rule so changes to workflow files require human review, a branch protection rule that prevents unreviewed workflow edits from merging, and an org-level policy that caps what permissions an agentic workflow can request. By the end, your workflow will be protected against accidental or unauthorised changes in a team or enterprise setting.

## 📋 Before You Start

- You have at least one compiled and running agentic workflow from [Manage Costs and AI Credit Budgets](26-manage-costs-and-budgets.md) or the [What's Next? Keep Exploring](14-next-steps.md) hub.
- You have admin access to your practice repository (or permission to open a pull request for a repository admin to approve).

> [!NOTE]
> <details>
> <summary><b>Enterprise users: governance applies at the organisation level too.</b></summary>
>
> On GitHub Enterprise Cloud (GHEC) and GitHub Enterprise Server (GHES) 3.12+, organisation administrators can enforce [repository rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) across every repository in the org — not just one. The steps below work in a single repository, but your admin can promote the same patterns to org-wide rulesets. Ask your GitHub Enterprise admin whether org-level rulesets are already in place before creating repository-level rules that might conflict.
>
> </details>

## Steps

### Require code review for workflow file changes

`CODEOWNERS` assigns automatic reviewers whenever specific files change in a pull request. Add a rule that covers your `.github/workflows/` directory so every workflow edit needs human sign-off.

Open (or create) `.github/CODEOWNERS` in your practice repository:

<details>
<summary>🖥️ GitHub UI path</summary>

1. In your repository on GitHub, click **Add file** → **Create new file**.
2. Set the file path to `.github/CODEOWNERS`.
3. Add the content below and click **Commit new file**.

</details>

```text
# Require review for all agentic workflow files
.github/workflows/   @your-github-username
```

Replace `@your-github-username` with your own username (or a team such as `@your-org/workflow-admins`). This rule means any pull request that modifies a file under `.github/workflows/` will automatically request your review before it can merge.

> [!TIP]
> You can list multiple reviewers or teams, space-separated: `.github/workflows/ @alice @your-org/platform-team`. The first reviewer in the list is the _primary_ code owner; all listed parties receive the review request.

### Enable branch protection and required reviews

A `CODEOWNERS` file only assigns reviewers — it doesn't enforce that the review is completed. Add a branch protection rule to prevent merging without an approved review.

1. Go to **Settings** → **Branches** in your repository.
2. Click **Add branch ruleset** (or **Add rule** on older GitHub versions).
3. Set **Branch name pattern** to `main` (or your default branch).
4. Enable **Require a pull request before merging**.
5. Under that option, enable **Require review from Code Owners**.
6. Click **Save changes**.

Now any pull request that touches `.github/workflows/` must be approved by a code owner before it can merge. This prevents direct pushes of modified workflow files — including agentic workflow `.md` files and their compiled `.lock.yml` counterparts.

> [!TIP]
> On GHES or GHEC, your admin may already manage these rules as org-level rulesets. Before adding a repository rule, check **Settings** → **Rules** → **Rulesets** to see whether an inherited ruleset already covers your branch.

### Limit workflow permissions with an org policy

Even with review gates, it's good practice to restrict what permissions an agentic workflow can _request_ at the org level. This prevents a workflow author from requesting `contents: write` when `contents: read` is sufficient.

On GitHub Enterprise Cloud:

1. In your organisation settings, go to **Actions** → **General**.
2. Under **Workflow permissions**, choose **Read repository contents and packages permissions** (the most restrictive default).
3. Disable **Allow GitHub Actions to create and approve pull requests** unless your team explicitly needs it.

This setting makes `contents: write` and `pull-requests: write` _opt-in_ — a workflow author must explicitly request elevated permissions in frontmatter, and that change goes through your CODEOWNERS review gate before it can merge.

<details>
<summary>🖥️ If you don't have org admin access</summary>

1. Note the two settings above and share them with your GitHub Enterprise admin.
2. Ask the admin to confirm what the current org-level default is.
3. For your own practice repository, go to **Settings** → **Actions** → **General** and set **Workflow permissions** to **Read repository contents and packages permissions**.

</details>

### Verify the governance chain

Test that the controls work end-to-end:

1. Create a new branch in your practice repository.
2. Make a small edit to your agentic workflow `.md` file (add a comment, for example).
3. Open a pull request targeting `main`.
4. Confirm the pull request shows **Review required** and lists you (or your team) as the code owner who must approve.
5. Merge only after approving — or ask a colleague to open the PR and approve it yourself.

If the PR merges without requiring a review, double-check that the CODEOWNERS path matches your workflow file location exactly (paths in CODEOWNERS are relative to the repository root and are case-sensitive).

## ✅ Checkpoint

- [ ] `.github/CODEOWNERS` exists and assigns a reviewer to `.github/workflows/`
- [ ] A branch protection rule (or ruleset) requires code owner approval before merging to `main`
- [ ] Your org's (or repository's) default workflow permissions are set to read-only
- [ ] You opened a test pull request that correctly triggered the CODEOWNERS review request
- [ ] You can explain why a compiled `.lock.yml` file should be included in the governance scope alongside its source `.md` file
- [ ] You know whether org-level rulesets are already in place in your enterprise environment

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

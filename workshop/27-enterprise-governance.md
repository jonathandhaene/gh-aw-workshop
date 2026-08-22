<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Govern Agentic Workflows Across Your Organisation

> _When dozens of teams run agentic workflows, consistent defaults and guardrails matter — `gh aw env` gives you a single place to set them._

## 🎯 What You'll Do

Configure organisation-level and repository-level defaults for your agentic workflows using `gh aw env`. By the end of this step, you will understand the three configuration scopes (enterprise, organisation, repository), know which settings can be locked at each scope, and have applied at least one org-level default to your practice setup.

## 📋 Before You Start

- You have a working scheduled workflow from [Test and Improve Your Workflow](12-test-and-iterate.md).
- You have owner or admin access to the organisation (or a personal namespace) where your practice repository lives.
- GHES users: confirm your instance is 3.12 or later. See [Enterprise Setup Considerations](side-quest-enterprise-setup.md).

## Steps

### Understand the three configuration scopes

gh-aw reads configuration from three nested scopes. A narrower scope always wins over a broader one.

| Scope | Who sets it | Example use |
|---|---|---|
| **Enterprise** | Enterprise admin | Maximum AI credit budgets, approved model list |
| **Organisation** | Org owner | Default model, runner label, token policies |
| **Repository** | Repo maintainer | Override model for a specific project |

Settings flow downward: an enterprise setting that is not overridden becomes the org default, which in turn becomes each repository's default. An org owner can lock a setting so repositories cannot override it.

> [!NOTE]
> On GitHub Enterprise Server, the enterprise scope is only available to site administrators. On github.com, it applies to GitHub Enterprise Cloud organisations enrolled in enterprise managed users (EMU).

### View your current environment settings

Run this command in any repository that has a compiled workflow:

```bash
gh aw env list
```

You will see a table of keys, their current values, and the scope where each value was resolved. A `(locked)` marker means a higher scope has prevented overrides at lower levels.

### Set an organisation-level default

Pick a setting to apply across all repositories in your organisation. The most common starting points are the default AI model and a maximum AI credit limit.

Set the default model at the organisation scope:

```bash
gh aw env set --org YOUR_ORG_NAME default-model "github/copilot-4o"
```

Set a per-run AI credit cap that applies to every workflow in the org unless a repository overrides it:

```bash
gh aw env set --org YOUR_ORG_NAME max-ai-credits 800
```

Replace `YOUR_ORG_NAME` with your organisation's GitHub handle (or your personal account name when practising on github.com).

> [!TIP]
> `gh aw env set` only changes the stored configuration — it does not recompile your workflows. Run `gh aw compile` in each affected repository after changing org-level defaults if you want to verify the resolved values before the next scheduled run.

### Lock a setting to prevent repository overrides

An org owner can lock any setting. Once locked, repository-level `gh aw env set` calls for that key are silently ignored, and `gh aw env list` shows `(locked)` next to the value.

```bash
gh aw env set --org YOUR_ORG_NAME --lock max-ai-credits 800
```

Use locking sparingly. It is most appropriate for budget caps and security-sensitive settings (such as disabling network access or restricting tool lists) that individual teams should not be able to circumvent.

### Override a setting at repository scope

When a team needs a higher AI credit limit for a data-intensive workflow, they can set a repository override — unless the org has locked the key:

```bash
gh aw env set --repo YOUR_ORG_NAME/YOUR_REPO_NAME max-ai-credits 2000
```

Verify the resolved value:

```bash
gh aw env list --repo YOUR_ORG_NAME/YOUR_REPO_NAME
```

The `max-ai-credits` row should now show `2000` with scope `repository`.

### Remove a setting to fall back to a higher scope

Deleting a repository-level key restores the organisation default:

```bash
gh aw env unset --repo YOUR_ORG_NAME/YOUR_REPO_NAME max-ai-credits
```

Run `gh aw env list` again to confirm the value reverts to the org-level setting.

### Common governance patterns for enterprise teams

| Goal | Recommended setting | Scope |
|---|---|---|
| Limit runaway spending | `max-ai-credits` + `max-daily-ai-credits` | Organisation (locked) |
| Standardise the model | `default-model` | Organisation |
| Target a runner fleet | `runs-on` label list | Organisation |
| Block external network access | `network: none` in frontmatter | Organisation (locked) |
| Allow specific runners only | `self-hosted-runner-group` | Enterprise or Organisation |

For a full list of keys that `gh aw env` accepts, see the [gh-aw governance guide](https://github.github.com/gh-aw/guides/governance/).

## ✅ Checkpoint

- [ ] You ran `gh aw env list` and read the resolved values and their scopes
- [ ] You set at least one organisation-level default (model or credit cap) with `gh aw env set --org`
- [ ] You verified the setting appears in `gh aw env list` with scope `organisation`
- [ ] You can explain the difference between a locked and an unlocked org setting
- [ ] You know which settings your enterprise or organisation restricts and why
- [ ] GHES only: you confirmed with your administrator that `gh aw env` is available on your instance version

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

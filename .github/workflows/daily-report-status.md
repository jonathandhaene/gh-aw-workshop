---
name: Daily Report Status
on:
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  github:
    mode: gh-proxy
    toolsets: [issues, pull_requests, repos]
safe-outputs:
  create-issue:
---

Create one concise repository activity report in a new issue.

Cover only activity from the previous 24 hours:

- commits pushed
- issues opened, closed, or updated
- pull requests opened, merged, closed, or updated

Highlight the most important activity and add one sentence explaining why it matters
to the team. Do not include a static repository or workshop-content inventory. If no
qualifying activity occurred, state that clearly instead of filling the report with
older or unrelated information.
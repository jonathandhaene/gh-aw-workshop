---
name: Daily Report Status
on:
  workflow_dispatch:
permissions:
  contents: read
  issues: read
safe-outputs:
  create-issue:
---

Generate an activity report in a new issue.
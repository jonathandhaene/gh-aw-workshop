# One-Day Workshop Program

A full-day hands-on experience with GitHub Agentic Workflows — from your first workflow to a live hackathon project.

## Program

| Time | Session |
|------|---------|
| 09:30 | **Welcome and setup** — introductions, environment check, and a tour of what you'll build today |
| 10:00 | **Workshop — Part 1** — build your first agentic workflow: write, compile, run, and iterate ([Start here →](00-welcome.md)) |
| 11:30 | **Share and reflect** — each participant shows their workflow; group discussion on what surprised you |
| 12:00 | **Lunch** |
| 13:00 | **Deep dive — Agenticism** — how agentic workflows shift responsibility from shell scripts to AI reasoning; architecture, safety model, and cost model ([Agentic Workflows Deep Dive →](side-quest-05-02-aw-deep-dive.md)) |
| 13:30 | **Workshop — Part 2** — go deeper: conditional logic, live data sources, MCP tools, memory, and sub-agents ([Continue →](14-next-steps.md)) |
| 14:30 | **Share and reflect** — demos of Part 2 workflows; group discussion on patterns and tradeoffs |
| 15:00 | **Hackathon** — build something real: pick a workflow idea from your own team or repo and ship it |
| 16:15 | **Share and reflect** — each group demos their hackathon project and explains one decision they made |
| 16:30 | **Social time** |

## Before You Arrive

Make sure you have completed the [prerequisites](01-prerequisites.md) before the day starts. The morning session moves fast, and environment issues will slow you down.

## 📋 Before You Use This Agenda

This agenda is for facilitators running a full-day in-person or virtual workshop. Before the session starts, confirm:

- Participants have completed [01-prerequisites.md](01-prerequisites.md) and have access to GitHub Copilot.
- Each participant has a working Codespace **or** a local terminal with `gh` and Node.js installed (see [02a-setup-codespace.md](02a-setup-codespace.md) or [02b-setup-local.md](02b-setup-local.md)).
- `gh aw` is installed and returns a version string for each participant:

  ```bash
  gh aw --version
  ```

- The workshop schedule has been shared with participants in advance.

## Facilitation Notes

**Part 1** covers [Steps 0–12](README.md#curriculum--part-1-build-your-first-workflow): welcome, prerequisites, environment setup, Actions intro, the agentic workflow model, installing `gh-aw`, writing your first workflow, running it, and iterating.

**Part 2** is self-directed. Participants choose their own path through [Steps 14+](README.md#curriculum--part-2-go-deeper) based on what interests them most. Facilitators circulate and help unblock.

**Hackathon** has no prescribed outcome — the goal is one working workflow that solves a real problem. Teams of two or three work well.

## ✅ Checkpoint

Before the session begins, confirm each item with your co-facilitators:

- [ ] All participants have completed [01-prerequisites.md](01-prerequisites.md) and can access their GitHub account.
- [ ] Each participant has a working Codespace or local terminal — run `gh aw --version` to verify.
- [ ] The workshop repository is accessible to all participants.
- [ ] The day's schedule has been shared and participants know where to start ([00-welcome.md](00-welcome.md)).
- [ ] You have a plan for the hackathon segment: teams formed, problem ideas ready or brainstormed in advance.

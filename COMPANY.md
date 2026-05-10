---
name: Agile Flow
description: Complete Agile delivery framework for Paperclip — agents, skills, and workflows derived from vibeacademy/agile-flow.
slug: agile-flow
schema: agentcompanies/v1
version: 0.1.0
license: BUSL-1.1
authors:
  - name: VibeAcademy
    url: https://github.com/vibeacademy
goals:
  - Provide a complete Agile delivery team as Paperclip agents and skills
  - Stay aligned with upstream vibeacademy/agile-flow and vibeacademy/agile-flow-gcp
  - Enable workshop participants to bootstrap a working agentic project quickly
metadata:
  sources:
    - kind: github-repo
      repo: vibeacademy/agile-flow
      attribution: VibeAcademy
      license: BUSL-1.1
      usage: referenced
    - kind: github-repo
      repo: vibeacademy/agile-flow-gcp
      attribution: VibeAcademy
      license: BUSL-1.1
      usage: referenced
---

# Agile Flow for Paperclip

This package contains 9 agent definitions and 28 skills that together implement the Agile Flow methodology as a Paperclip company. The agents map roughly to a small product engineering org — Product Manager, Product Owner, System Architect, Engineer, PR Reviewer, Quality Engineer, and three DevOps Engineer variants (multi-platform / GCP / AWS).

The org chart, suggested skill attachments per agent, and import instructions live in [`README.md`](README.md) and [`docs/attachments.md`](docs/attachments.md).

Generated from [vibeacademy/agile-flow](https://github.com/vibeacademy/agile-flow) and [vibeacademy/agile-flow-gcp](https://github.com/vibeacademy/agile-flow-gcp). Built for [Paperclip](https://github.com/paperclipai/paperclip).

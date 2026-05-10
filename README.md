# Paperclip — Agile Flow

A complete Agile delivery framework packaged for [Paperclip](https://github.com/paperclipai/paperclip): **9 agent definitions**, **28 skills**, and **3 vendored scripts** derived from [vibeacademy/agile-flow](https://github.com/vibeacademy/agile-flow) and [vibeacademy/agile-flow-gcp](https://github.com/vibeacademy/agile-flow-gcp).

The repo follows the [Agent Companies specification](https://agentcompanies.io/specification) (`agentcompanies/v1`), so it can be imported skill-by-skill today and as a whole company package once Paperclip's `company import` matures.

## Quick start

### Importing skills

In Paperclip, open **Company → Skills → +**. Three options work:

1. **Shorthand** — paste `vibeacademy/paperclip/<skill-slug>` (e.g. `vibeacademy/paperclip/groom-backlog`). Resolves to `https://github.com/vibeacademy/paperclip/skills/<slug>/SKILL.md`.
2. **Full GitHub URL** — `https://github.com/vibeacademy/paperclip/blob/main/skills/groom-backlog/SKILL.md`.
3. **Search GitHub** — Paperclip's UI can also search for `SKILL.md` repos.

### Importing agents

Agents are imported per-agent in Paperclip's UI. For each agent in `agents/`, open the matching agent in Paperclip → **Instructions** tab → **+** → paste the body of the corresponding `AGENTS.md` (or upload the file). Set `name`, `title`, `reportsTo`, and skill attachments via the agent's other tabs.

A future `paperclip company import --from <repo>` will pull the whole package in one shot — the layout here is forward-compatible.

## What's in here

### `agents/` — 9 role definitions

| Slug | Role |
|---|---|
| `product-manager` | Strategic Product Manager — vision, market fit, go/no-go decisions |
| `product-owner` | Product Owner / Backlog Prioritizer — CD3, Definition of Ready, Ready column |
| `system-architect` | System Architect — distributed systems, DDD, ADRs |
| `engineer` | Senior Full-Stack Engineer — picks Ready tickets, opens PRs |
| `pr-reviewer` | Staff Engineer / PR Reviewer — written GO/NO-GO, decision support only |
| `quality-engineer` | Quality Engineer — BDD test plans, defect documentation |
| `devops-engineer` | DevOps Engineer (multi-platform: Render / Cloudflare / Vercel / Railway / Fly.io) |
| `devops-engineer-gcp` | DevOps Engineer (GCP — Cloud Run, GKE, App Engine, Cloud SQL) |
| `devops-engineer-aws` | DevOps Engineer (AWS — ECS, Lambda, EKS, App Runner, RDS) |

### `skills/` — 28 procedural skills

**Always available** (recommend on most agents):

- `commit` — Conventional Commits

**Tactical workflow** (Engineer / PO / Reviewer):

- `work-ticket`, `quick-fix`, `create-ticket`, `groom-backlog`, `sprint-status`, `check-milestone`, `review-pr`, `test-feature`

**Strategic / product** (PM):

- `evaluate-feature`, `release-decision`, `research`, `jtbd`, `positioning`, `lock-scope`

**Architectural** (Architect):

- `architect-review`

**Bootstrap** (one-shot project setup):

- `bootstrap-product`, `bootstrap-architecture`, `bootstrap-agents`, `bootstrap-workflow`

**Diagnostics & journaling**:

- `doctor`, `log-session`

**Memory MCP-only** (skip in Paperclip — kept for completeness):

- `prune-memory`, `validate-memory`

**Script-bundling** (each ships a vendored shell script):

- `template-sync` — sync framework files from upstream Agile Flow release (opens PR)
- `pull-upstream` — apply upstream main into a fork (commits locally, GCP fork pattern)
- `diagnose-cloudrun` — read-only Cloud Run diagnostic snapshot (GCP)

See [`docs/attachments.md`](docs/attachments.md) for the suggested skill → agent mapping.

## What got dropped from upstream

These were intentionally not migrated:

- **Bootstrap & CI shell scripts** (`setup-accounts.sh`, `provision-gcp-project.sh`, `workshop-setup.sh`, `lint-agent-policies.sh`, `verify-bot-permissions.sh`, etc.) — one-shot infra / CI tooling, not agent runtime work. They live in the source repos.
- **GCP-only fork-management commands** that don't bundle a script (`triage-downstream-feedback`).
- **Test files** (`*.test.sh`).
- **Claude Code-specific frontmatter fields** (`color`, `model`, embedded `<example>` dispatch hints) — Paperclip configures runtime in the agent's UI tabs.
- **Memory MCP integration** in the agent bodies — Paperclip uses task/comment threading instead. The two MCP-specific skills are kept but flagged.

## Compatibility notes

- **`{worker-bot}` and `{reviewer-bot}` placeholders** in `agents/engineer/AGENTS.md` and `agents/pr-reviewer/AGENTS.md` need to be filled in (or the bot-account `gh auth switch` block stripped) before import.
- **Project-specific doc references** (`docs/PRODUCT-REQUIREMENTS.md`, `docs/PATTERN-LIBRARY.md`, `CLAUDE.md`, etc.) inside agent and skill bodies refer to docs in the source projects. Either copy those docs into the consuming Paperclip company or scrub the references.
- **`gh` CLI** is referenced throughout. Configure GitHub access via `.paperclip.yaml` env (`GH_TOKEN`) on agents that need it (Engineer, PR Reviewer, DevOps).

## License

[Business Source License 1.1](LICENSE), matching upstream. Becomes Apache 2.0 on 2030-05-10.

Source attribution preserved in each file's footer.

## See also

- [vibeacademy/agile-flow](https://github.com/vibeacademy/agile-flow) — upstream framework
- [vibeacademy/agile-flow-gcp](https://github.com/vibeacademy/agile-flow-gcp) — GCP variant
- [Agent Companies specification](https://agentcompanies.io/specification)
- [Paperclip](https://github.com/paperclipai/paperclip)

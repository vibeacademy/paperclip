# Skill Attachments

Suggested skill attachments per agent. Apply these in each agent's **Skills** tab in the Paperclip UI. The `paperclip` skill is auto-attached to every agent — it isn't listed below.

## Attached to every agent

- `agile-ontology` — foundational vocabulary (Ticket, Epic, Milestone, PR, Review, Release, ADR + invariants). Every agent should read this so handoffs use shared terms and invariants get enforced consistently.

Per-role attachments below build on top of `agile-ontology`.

## Product Manager

Strategic decisions: vision, market fit, go/no-go.

- `evaluate-feature`
- `release-decision`
- `research`
- `jtbd`
- `positioning`
- `lock-scope`
- `bootstrap-product` *(one-shot, can detach after use)*

## Product Owner

Backlog grooming, ticket quality, Definition of Ready.

- `groom-backlog`
- `create-ticket`
- `sprint-status`
- `check-milestone`
- `bootstrap-workflow` *(one-shot, can detach after use)*

## System Architect

Architectural guidance, design review, ADRs.

- `architect-review`
- `bootstrap-architecture` *(one-shot, can detach after use)*
- `bootstrap-agents` *(one-shot, can detach after use)*

## Engineer

Implements Ready tickets, opens PRs.

- `work-ticket`
- `quick-fix`
- `commit`
- `log-session`

## PR Reviewer (Staff Engineer)

Reviews PRs, posts written GO/NO-GO recommendations.

- `review-pr`
- `commit`

## Quality Engineer (Tester)

BDD test plans, test execution, defect documentation.

- `test-feature`

## DevOps Engineer (multi-platform / GCP / AWS)

Deployments, preview environments, CI/CD health, infrastructure.

- `doctor`
- `template-sync` *(if maintaining a fork)*
- `upgrade` *(alternative to template-sync, runs the same script)*

**GCP variant** also gets:
- `pull-upstream` *(if maintaining a downstream fork like agile-flow-gcp)*
- `diagnose-cloudrun`

**AWS variant** has no AWS-specific skills in this package yet.

## Skills not attached by default

These are kept in the package but not recommended for default attachment:

| Skill | Why skipped |
|---|---|
| `prune-memory` | Memory MCP-only; Paperclip uses task/comment threading |
| `validate-memory` | Memory MCP-only; same reason |

Attach manually if you have a Memory MCP server configured for your Paperclip company.

## Notes

- **One-shot skills** (the four `bootstrap-*` skills, `lock-scope`) are useful during initial project setup. After bootstrap is complete you can detach them to keep each agent's skill list focused.
- **`commit`** is attached to multiple agents because anything that produces git commits benefits from consistent Conventional Commit formatting.
- The `paperclip` built-in skill is auto-attached to every agent and not listed here.

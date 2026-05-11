---
name: agile-ontology
description: Shared vocabulary for agile delivery — Ticket, Epic, Milestone, PullRequest, Review, Release, ArchitectureDecision — with relationships, lifecycle states, and invariants. Read at the start of any task involving these entities so your reasoning aligns with the other agents on the team.
---

# Agile Delivery Ontology

This skill defines the shared vocabulary every agent on this team uses to reason about work. When you read a ticket, hand off to another agent, or check whether something is allowed to move forward, you're operating on these entities.

**This is reference material**, not a procedure. Read it at the start of a session and refer back when you're unsure how an entity is structured, what state transition is allowed, or whether an action would violate an invariant.

The Zod schemas in this document are presented as a precise specification of each entity's shape. You are not expected to execute them — they exist so every agent has the same mental model of the data, and so future runtime validation can use them as-is.

---

## The seven entities

### 1. Ticket — the atomic unit of work

One Ticket = one deployable change = one PullRequest. If you can't deliver it in a single PR, decompose it.

```typescript
import { z } from 'zod';

export const TicketStatus = z.enum([
  'icebox', 'backlog', 'ready', 'in_progress', 'in_review', 'done'
]);
export const Priority = z.enum(['P0', 'P1', 'P2', 'P3']);
export const Size = z.enum(['S', 'M', 'L', 'XL']);

export const PowerSections = z.object({
  environment_context: z.string().min(1),   // A — repo paths, stack, files to modify
  guardrails: z.string().min(1),            // B — hard constraints, things NOT to do
  happy_path: z.string().min(1),            // C — Input -> Logic -> Output, one branch
  definition_of_done: z.string().min(1),    // D — concrete assertions, lint/test commands
});

export const Ticket = z.object({
  id: z.string(),                           // e.g. GitHub issue number
  title: z.string().min(1),
  problem_statement: z.string(),
  status: TicketStatus,
  priority: Priority,
  size: Size,
  effort_days: z.number().positive(),
  cd3_score: z.number().min(0).max(10).optional(),
  parent_epic: z.string().optional(),
  milestone: z.string().optional(),
  depends_on: z.array(z.string()).default([]),
  blocks: z.array(z.string()).default([]),
  follows_from_adr: z.array(z.string()).default([]),
  power_sections: PowerSections,
  ready_eligible: z.boolean(),              // true iff all Power Sections populated
});
export type Ticket = z.infer<typeof Ticket>;
```

**Field notes:**

- **Power Sections (A–D)**: every Ticket promoted to `ready` must have all four populated and non-vague. This is the canonical scope contract for an agent implementing the work. See [Invariant 1](#invariants).
- **Priority**: P0 = critical (blocks release), P1 = important, P2 = nice-to-have, P3 = backlog/icebox candidate.
- **Size**: S/M/L/XL. XL Tickets must be decomposed before they can move to `ready`. See [Invariant 5](#invariants).
- **CD3 score**: Cost of Delay / Duration. Used by the Product Owner for objective prioritization during grooming.
- **`ready_eligible`**: a derived boolean — true only when every Power Section is present and concrete. Treat it as the gate for the `backlog -> ready` transition.

**Lifecycle:**

```
icebox  ←→  backlog  →  ready  →  in_progress  →  in_review  →  done
                                                                 ↑
                                       (humans merge the PR, then move to done)
```

Allowed transitions:
- `icebox ↔ backlog` — re-prioritization
- `backlog → ready` — gated by `ready_eligible === true` (Invariant 1)
- `ready → in_progress` — agent picks it up (Engineer)
- `in_progress → in_review` — PR opened
- `in_review → done` — **human** merges (never an agent)

---

### 2. Epic — a coherent capability

Groups related Tickets. Use Epics to track work that delivers user value end-to-end.

```typescript
export const EpicStatus = z.enum(['planning', 'in_progress', 'done', 'abandoned']);

export const Epic = z.object({
  id: z.string(),
  title: z.string().min(1),
  vision: z.string(),                       // what users get when this is done
  status: EpicStatus,
  child_tickets: z.array(z.string()).default([]),
  success_criteria: z.array(z.string()),    // measurable
  depends_on_epics: z.array(z.string()).default([]),
  milestone: z.string().optional(),
});
export type Epic = z.infer<typeof Epic>;
```

An Epic is `done` if and only if every Ticket in `child_tickets` is `done`. See [Invariant 4](#invariants).

---

### 3. Milestone — a roadmap target with a date

```typescript
export const MilestoneStatus = z.enum(['planned', 'on_track', 'at_risk', 'achieved', 'missed']);

export const Milestone = z.object({
  id: z.string(),
  name: z.string().min(1),
  target_date: z.string().date(),
  projected_completion: z.string().date().optional(),  // updated by check-milestone
  exit_criteria: z.array(z.string()),
  critical_path_tickets: z.array(z.string()).default([]),
  status: MilestoneStatus,
});
export type Milestone = z.infer<typeof Milestone>;
```

A Milestone is `at_risk` when `projected_completion > target_date`. The `check-milestone` skill computes this and updates `status`.

---

### 4. PullRequest — the code-review artifact

```typescript
export const CiStatus = z.enum(['pending', 'passing', 'failing', 'cancelled']);

export const PullRequest = z.object({
  id: z.string(),                           // e.g. PR number
  ticket_id: z.string(),                    // the Ticket this resolves
  branch: z.string(),                       // feature/issue-NN-...
  author_agent: z.string(),                 // slug of agent that opened it
  ci_status: CiStatus,
  description: z.string(),
  url: z.string().url().optional(),
});
export type PullRequest = z.infer<typeof PullRequest>;
```

A PR is **mergeable** when:
1. `ci_status === 'passing'`
2. there exists a `Review` with `outcome === 'GO'` and `reviewer_agent !== author_agent` (Invariant 2)
3. all required-changes from any prior Review have been addressed

Agents never merge. Humans do.

---

### 5. Review — the written go/no-go assessment of a PR

```typescript
export const ReviewOutcome = z.enum(['GO', 'NO-GO', 'CONDITIONAL']);

export const Review = z.object({
  id: z.string(),
  pull_request_id: z.string(),
  reviewer_agent: z.string(),               // slug — must differ from PR author
  outcome: ReviewOutcome,
  required_changes: z.array(z.string()).default([]),
  suggestions: z.array(z.string()).default([]),
  rationale: z.string(),
  posted_at: z.string().datetime(),
});
export type Review = z.infer<typeof Review>;
```

A Review is **decision support** — it never approves or merges via the GitHub UI. The `review-pr` skill is the canonical producer.

---

### 6. Release — a batch of Epics shipped together

```typescript
export const ReleaseDecision = z.enum(['GO', 'NO-GO', 'CONDITIONAL_GO']);

export const ReadinessCheck = z.object({
  product_quality: z.enum(['pass', 'warn', 'fail']),
  market_readiness: z.enum(['pass', 'warn', 'fail']),
  business_readiness: z.enum(['pass', 'warn', 'fail']),
  communication_readiness: z.enum(['pass', 'warn', 'fail']),
});

export const Release = z.object({
  id: z.string(),
  version: z.string(),                      // e.g. v2.0
  scope_epics: z.array(z.string()),
  decision: ReleaseDecision,
  readiness: ReadinessCheck,
  open_p0_count: z.number().int().nonnegative(),
  blockers: z.array(z.string()).default([]),
  rollback_plan: z.string(),
  target_date: z.string().date().optional(),
});
export type Release = z.infer<typeof Release>;
```

A Release is `GO` only if all four `readiness` fields are `pass`, `open_p0_count === 0`, and `rollback_plan` is non-empty. See [Invariant 3](#invariants).

---

### 7. ArchitectureDecision (ADR)

```typescript
export const AdrStatus = z.enum(['proposed', 'accepted', 'deprecated', 'superseded']);

export const Option = z.object({
  name: z.string(),
  pros: z.array(z.string()),
  cons: z.array(z.string()),
  rejected: z.boolean().default(false),
  rejection_reason: z.string().optional(),
});

export const ArchitectureDecision = z.object({
  id: z.string(),                           // e.g. ADR-042
  title: z.string().min(1),
  status: AdrStatus,
  context: z.string(),
  options_considered: z.array(Option),
  decision: z.string(),
  consequences_positive: z.array(z.string()),
  consequences_negative: z.array(z.string()),
  risks: z.array(z.string()).default([]),
  superseded_by: z.string().optional(),
});
export type ArchitectureDecision = z.infer<typeof ArchitectureDecision>;
```

Produced by `architect-review`. Tickets reference ADRs via `follows_from_adr` when their implementation is constrained by a prior architectural choice.

---

## Relationships

```
            ┌─────────────────┐
            │ ArchitectureDec │
            └────────┬────────┘
                     │ follows_from
                     ▼
   ┌─────┐    ┌──────────┐    ┌──────────┐
   │Epic │◄───│  Ticket  │───►│ Milestone│
   └──┬──┘    └─────┬────┘    └──────────┘
      │             │
      │             │ resolves
      ▼             ▼
  ┌────────┐   ┌─────────────┐    ┌────────┐
  │Release │   │ PullRequest │◄───│ Review │
  └────────┘   └─────────────┘    └────────┘
```

| From | Relationship | To | Cardinality |
|---|---|---|---|
| Ticket | belongs_to | Epic | many-to-one (optional) |
| Ticket | targets | Milestone | many-to-one (optional) |
| Ticket | depends_on | Ticket | many-to-many |
| Ticket | follows_from | ADR | many-to-many |
| PullRequest | resolves | Ticket | one-to-one |
| Review | assesses | PullRequest | many-to-one (multiple reviews allowed) |
| Release | includes | Epic | many-to-many |

---

## Invariants

These are the rules every agent must respect. If you find yourself about to violate one, **stop and escalate** — don't work around it silently.

1. **Ready requires Power Sections.** A Ticket cannot move to `ready` unless `ready_eligible === true` — i.e., Power Sections A, B, C, and D are all populated with concrete content (not template placeholders, not "TBD").

2. **Independent review.** A PR is not mergeable without a Review whose `outcome === 'GO'` *and* `reviewer_agent !== author_agent`. The Engineer who wrote the code cannot also review it.

3. **No P0 in released scope.** A Release cannot have `decision === 'GO'` while `open_p0_count > 0` against any Ticket in its `scope_epics`.

4. **Epic completion is total.** An Epic is `done` if and only if every Ticket in `child_tickets` is `done`. Not "most." Not "MVP-complete." Every.

5. **Decomposition before promotion.** Tickets with `size === 'XL'` cannot move to `ready`. Decompose into Tickets of size L or smaller first.

6. **Scope locks bind.** Once a Release's scope is locked (via the `lock-scope` skill), new Tickets can be added to it only via a documented trade-off — something else must be cut.

7. **Single happy path.** A Ticket's Power Section C describes exactly one happy path with no major branch points. Tickets with multiple branches must be split into separate Tickets.

8. **No direct main commits.** All Ticket work happens on a feature branch and is delivered via a PullRequest. Agents never commit directly to `main`.

9. **Humans merge, agents don't.** No agent — Engineer, PR Reviewer, or otherwise — clicks Merge or moves a Ticket to `done`. Only humans do.

---

## How specific skills produce or consume entities

| Skill | Produces | Consumes | Invariants enforced |
|---|---|---|---|
| `create-ticket` | Ticket | — | 1 (Power Sections), 7 (single happy path) |
| `groom-backlog` | — (mutates Ticket.status) | Ticket, Epic | 1, 5 |
| `work-ticket` | PullRequest | Ticket | 7, 8 |
| `review-pr` | Review | PullRequest, Ticket | 2 (independence) |
| `release-decision` | Release | Epic, Ticket | 3 (no P0), 9 |
| `architect-review` | ArchitectureDecision | — | — |
| `evaluate-feature` | (decision → Epic creation) | — | — |
| `sprint-status` | (report) | Ticket, PullRequest | — |
| `check-milestone` | (mutates Milestone.projected) | Milestone, Ticket | — |
| `lock-scope` | (Release.scope locked) | Epic | 6 |
| `quick-fix` | PullRequest (no Ticket) | — | 8 — *exception:* no Ticket binding |
| `commit` | (a commit on a branch) | — | 8 |

---

## Handoff vocabulary

When an agent hands work to another agent — via a Paperclip task assignment, a comment, or a task description — **use the entity vocabulary explicitly**. Don't say "the feature stuff" or "that PR thing." Say:

- *"Ticket #45 (Epic 'User Onboarding') is now `in_review` as PR #108. Please produce a Review."*
- *"Milestone 'MVP Release' is `at_risk` (projected 2026-06-03, target 2026-05-29). Critical path: Tickets #21, #34, #41."*
- *"Release v2.0 decision is `CONDITIONAL_GO` pending Invariant 3 — there's one open P0 (#92)."*

This is the single biggest behavior change the ontology asks for: precise references instead of fuzzy noun phrases.

---

## What to do on an invariant violation

If you discover a Ticket, PR, or Release that violates an invariant:

1. **Stop.** Don't continue with the action that would extend the violation.
2. **State the violation precisely.** Cite the invariant by number and name the offending entity by ID.
3. **Choose one:**
   - **Fix the entity** if it's safe and within your role (e.g., decompose an XL ticket; populate a missing Power Section).
   - **Escalate** by posting a Paperclip comment that names the invariant and the entity, and assigning back to the agent or human responsible.

Don't silently route around invariants. They exist because past sessions discovered the failure modes the hard way.

---

## Where this ontology stops

This document defines **Layer 1: Delivery**. It deliberately does not define:

- **Layer 2: Product domain** — whatever the team is actually building (e.g., fitness studios, insurance policies, e-commerce orders). That layer is per-company and belongs in a separate skill specific to the product being shipped.
- **Layer 3: Paperclip primitives** — `Task`, `Comment`, `Approval`, `Routine` are owned by the Paperclip control plane. Your delivery entities ride on top of those (a Paperclip Task typically *carries* a Ticket reference), but Paperclip defines them, not us.

If your team's work needs Layer 2 reasoning (parsing a PRD into structured product features, validating user stories against business rules), create a sibling skill — e.g., `domain-ontology-fitness` — that defines product entities and explicitly references the Ticket entity defined here for the bridge to delivery.

<!-- Source: derived from vibeacademy/agile-flow + vibeacademy/agile-flow-gcp -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

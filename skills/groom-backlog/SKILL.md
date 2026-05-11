---
name: groom-backlog
description: Perform a backlog grooming session — re-prioritize using CD3, validate ticket scope, ensure Definition of Ready, and populate the Ready column with 2-5 well-defined tickets.
---

# Groom Backlog

Perform comprehensive backlog grooming on the project board.

## Ontology

Reads **Ticket** and **Epic** entities; mutates `Ticket.status`. Produces a **PrioritizationLogic** entity per Epic that records *why* Tickets were ordered the way they were (CD3 scores, dependencies, rationale). Consults the prior PrioritizationLogic for the same Epic so future grooming builds on prior reasoning instead of re-deciding from scratch. See [agile-ontology](../agile-ontology/SKILL.md). Enforces:

- **Invariant 1** — only Tickets with `ready_eligible === true` (all four Power Sections populated) may be promoted to `ready`.
- **Invariant 5** — Tickets with `size === 'XL'` must be decomposed before promotion to `ready`.

## Procedure

1. **Review Product Strategy**
   - Read `docs/PRODUCT-REQUIREMENTS.md` for current goals
   - Read `docs/PRODUCT-ROADMAP.md` for current phase and milestones
   - Verify backlog reflects strategic priorities

2. **Analyze Backlog Health**
   - Count tickets by status (Backlog, Ready, In Progress, In Review, Done, Icebox)
   - Assess ticket quality (descriptions, acceptance criteria, effort estimates)
   - Identify stale tickets (>30 days without activity)

3. **Prioritize Using CD3**
   - Calculate Cost of Delay / Duration for backlog items
   - Weight by user impact and business value
   - Consider feature dependencies

4. **Assess Ticket Scope**
   - For each ticket being promoted to Ready, check:
     - One ticket = one deployable change (single PR)
     - If >3 files for unrelated reasons → flag for decomposition
     - If environment context exceeds 4 sentences → flag for decomposition
     - If happy path has >1 major branch point → flag for splitting
     - If effort estimate is XL → recommend breaking into smaller tickets
   - Tickets that fail scoping should be decomposed on the spot (create child issues) rather than promoted to Ready

5. **Ensure Definition of Ready**
   - Verify top tickets have clear titles and descriptions
   - Confirm acceptance criteria are specific and testable
   - Check effort estimates and priority labels
   - Validate technical guidance is provided
   - Verify tickets include the 4 Power Sections (A. Environment Context, B. Guardrails, C. Happy Path, D. Definition of Done)
   - Reference `docs/TICKET-FORMAT.md` for the expected format

6. **Populate Ready Column**
   - Move top 2-5 well-defined tickets to Ready
   - Balance quick wins with strategic features
   - Ensure no blockers on Ready items

7. **Identify Issues**
   - Flag tickets needing refinement
   - Identify dependency conflicts
   - Note scope creep or misalignment with roadmap

## Output

Report:
- Backlog health metrics
- Top priorities moved to Ready
- Tickets needing refinement
- Scoping issues: tickets flagged for decomposition (too broad for single-PR agent implementation)
- Blockers and risks
- Recommendations for next grooming session

See `docs/TICKET-FORMAT.md` for the canonical ticket format specification.

### Output Format

End your output with a Result Block:

```
---

**Result:** Backlog groomed
Moved to Ready: 4 tickets (#21, #22, #23, #24)
Backlog remaining: 8 tickets
Flags: 2 tickets need refinement (#30, #31)
Next grooming: after current sprint completes
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

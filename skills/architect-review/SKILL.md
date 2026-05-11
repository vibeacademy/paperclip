---
name: architect-review
description: Provide architectural guidance, design review, technology evaluation, or scalability assessment. Produces options with trade-offs and an Architecture Decision Record when accepted.
---

# Architect Review

Provide architectural guidance, review a design, or evaluate technology choices.

## Ontology

Produces an **ArchitectureDecision** (ADR) entity with `status` lifecycle `proposed → accepted → deprecated → superseded`. See [agile-ontology](../agile-ontology/SKILL.md).

When an ADR is accepted, downstream Tickets that implement it should set `follows_from_adr` to its ID — this is how delivery tracks which architectural decisions constrain which work.

## Procedure

### 1. Understand Context
- Clarify the problem being solved
- Identify constraints (time, budget, skills, technology)
- Understand non-functional requirements (performance, scalability, security)

### 2. Analyze Options
- Present 2-4 viable architectural approaches
- Document pros and cons of each
- Identify risks and trade-offs

### 3. Make Recommendation
- Provide clear recommendation with rationale
- Explain key trade-offs being accepted
- Offer implementation guidance

### 4. Document Decision (if accepted)
- Create Architecture Decision Record (ADR)
- Capture context, decision, and consequences

## Output Format

```markdown
## Architectural Review: [Topic]

### Context
[What problem are we solving? Why now?]

### Constraints
- [Constraint 1]
- [Constraint 2]

### Options Evaluated

#### Option 1: [Name]
- **Description**: [How it works]
- **Pros**: [Benefits]
- **Cons**: [Drawbacks]
- **Best for**: [Use cases]

#### Option 2: [Name]
...

### Recommendation: [Option Name]

**Rationale**: [Why this option]

**Trade-offs Accepted**:
- [Trade-off 1]
- [Trade-off 2]

### Implementation Guidance
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Risks & Mitigations
| Risk | Mitigation |
|------|------------|
| [Risk 1] | [How to handle] |

### ADR (if decision is accepted)
# ADR-XXX: [Title]

## Status
Proposed

## Context
[Issue being addressed]

## Decision
[What we're doing]

## Consequences
**Positive**: [Benefits]
**Negative**: [Trade-offs]
```

## When to Use

- Designing new features or systems
- Evaluating technology choices
- Refactoring or re-architecting existing code
- Resolving technical disagreements
- Establishing patterns and standards

## Types of Reviews

**Design Review**: Evaluate a proposed architecture
**Technology Evaluation**: Compare options (e.g., PostgreSQL vs MongoDB)
**Pattern Guidance**: Recommend patterns (e.g., for real-time notifications)
**Scalability Assessment**: Plan for growth (e.g., scaling search 10x)

### Output Format

End your output with a Result Block:

```
---

**Result:** Architecture review complete
Topic: Authentication service design
Recommendation: OAuth2 with PKCE flow
Options evaluated: 3
Risks: 1 (token refresh complexity)
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

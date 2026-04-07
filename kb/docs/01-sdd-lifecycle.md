# The SDD Lifecycle

> **Last verified: 2026-04-04**

---

## Principle

SDD follows a 5-phase lifecycle: **Define, Decompose, Constrain, Generate, Verify**.
Each phase produces artifacts that feed the next. Verification failures loop back to
earlier phases -- you fix the spec, not the code.

---

## The Five Phases

### Phase 1: Define

**What it is:** Capture what you are building and why.

| Dimension | Detail |
|---|---|
| **Inputs** | Business need, user story, problem statement |
| **Outputs** | Requirements document (PRD-style), success criteria, scope boundaries |
| **Key activities** | Stakeholder alignment, acceptance criteria definition, explicit "out of scope" |
| **AI role** | Can help expand a brief into a full spec (e.g., Plan Mode in Claude Code), asks clarifying questions, identifies edge cases |
| **Human role** | Owns the "what" and "why", validates AI-expanded specs |

---

### Phase 2: Decompose

**What it is:** Break requirements into implementation-ready units with explicit
interfaces.

| Dimension | Detail |
|---|---|
| **Inputs** | Requirements document from Phase 1 |
| **Outputs** | Interface contracts (API schemas, type signatures), data models, component boundaries, task breakdown |
| **Key activities** | Architecture decisions, dependency mapping, generation sequence planning |
| **AI role** | Can suggest decomposition, but human validates architecture |
| **Human role** | Owns structural decisions, validates component boundaries |

**Key insight:** The decomposition determines the scope of each AI generation session.
LLMs perform better on focused, bounded tasks.

---

### Phase 3: Constrain

**What it is:** Add guardrails that prevent the AI from drifting.

| Dimension | Detail |
|---|---|
| **Inputs** | Design artifacts from Phase 2 |
| **Outputs** | Coding standards, framework conventions, security requirements, performance budgets, test criteria, explicit "never do" rules |
| **Key activities** | Define the three-tier permission model (Always / Ask First / Never -- from Addy Osmani) |
| **AI role** | Can help enumerate edge cases and constraints |
| **Human role** | Owns all constraints -- this is the human's primary leverage point |

**Key insight:** Without constraints, AI agents optimize for plausible-looking code,
not correct code.

---

### Phase 4: Generate

**What it is:** Feed the full spec package to the AI agent.

| Dimension | Detail |
|---|---|
| **Inputs** | Requirements + design + constraints (the "spec package") |
| **Outputs** | Generated code |
| **Key activities** | Context engineering, prompt structuring, iterative refinement loops |
| **AI role** | Primary actor -- writes code against the spec |
| **Human role** | Manages context, reviews output, decides when to refine spec vs. refine prompt |

This is the **only** phase where the AI writes code. All preceding phases are
preparation; all following phases are validation.

---

### Phase 5: Verify

**What it is:** Validate generated code against the original spec.

| Dimension | Detail |
|---|---|
| **Inputs** | Generated code + original spec |
| **Outputs** | Pass/fail determination + feedback for spec refinement |
| **Key activities** | Automated tests (written from spec), spec-compliance checks, LLM-as-judge patterns, human review for critical paths |
| **AI role** | Can self-verify, run tests, check compliance |
| **Human role** | Required for critical path review, architectural coherence |

**Key insight:** Verification is not optional. Without it, SDD degenerates into
"vibe coding with extra steps."

---

## Feedback Loops

When verification fails, the fix goes to the spec, not the code.

| Failure Type | Routes Back To | Action |
|---|---|---|
| Test failure | Phase 3 (Constrain) or Phase 1 (Define) | Refine constraints or clarify requirements |
| Architecture drift | Phase 2 (Decompose) | Revisit component boundaries |
| Spec ambiguity | Phase 1 (Define) | Add clarity, resolve `[NEEDS CLARIFICATION]` markers |
| Performance miss | Phase 3 (Constrain) | Tighten performance budgets |
| Security issue | Phase 3 (Constrain) | Add security constraints |

---

## Phase Artifacts Summary

| Phase | Artifact | Format | Example |
|---|---|---|---|
| Define | Requirements doc | Markdown PRD | "User can drag tasks between Kanban columns" |
| Define | Success criteria | Testable statements | "Drag-drop completes in <200ms" |
| Decompose | Interface contracts | OpenAPI / TypeScript types | `POST /api/tasks/{id}/move` |
| Decompose | Data models | Schema definitions | Task, Column, Board entities |
| Decompose | Task breakdown | Ordered list | "1. Create data model, 2. Build API, 3. Add UI" |
| Constrain | Coding standards | CLAUDE.md / .cursorrules | "Use React Server Components, no client state" |
| Constrain | "Never do" rules | Deny list | "Never use eval(), never import lodash" |
| Constrain | Test criteria | Acceptance scenarios | "Given task in column A, when dragged to B..." |
| Generate | Source code | Language-specific | TypeScript, Python, etc. |
| Verify | Test results | Pass/fail report | "14/15 tests passing, 1 failure in edge case" |
| Verify | Compliance report | Spec-diff | "Requirement 3.2 not implemented" |

---

## Mapping to Existing Methodologies

| SDD Phase | Agile Equivalent | Waterfall Equivalent | TDD Equivalent |
|---|---|---|---|
| Define | User Story + Acceptance Criteria | Requirements Phase | -- |
| Decompose | Sprint Planning + Technical Design | Design Phase | -- |
| Constrain | Definition of Done + Standards | Standards Phase | Write tests first |
| Generate | Implementation Sprint | Coding Phase | Make tests pass |
| Verify | Sprint Review + QA | Testing Phase | Refactor |

SDD is methodology-agnostic. It layers on top of Agile, Waterfall, or TDD rather
than replacing them.

---

## References

1. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

4. Fowler, M. "Context Engineering for Coding Agents." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html

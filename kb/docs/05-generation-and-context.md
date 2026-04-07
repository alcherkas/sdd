# Phase 4: Generation and Context Engineering

> **Last verified: 2026-04-04**

---

## Principle

The quality of generated code is determined by the quality and structure of context
provided to the AI agent. Context engineering -- the deliberate construction and
management of what the model sees -- is the core skill of the SDD practitioner [1][2].

---

## Context Engineering Fundamentals

### What the Model Sees Determines What It Produces

Martin Fowler defines context engineering as "building dynamic systems that provide the
right information and tools at the right time" to an AI model [1]. In the SDD workflow,
this means the spec, constraints, reference code, and conversation history collectively
determine the quality of generated output. A well-structured 3K token spec produces
better code than a rambling 10K token conversation.

### Context Budget Management

Every LLM has a finite context window. Not all context is equally valuable. The
practitioner's job is to maximize the signal-to-noise ratio within the available budget.

| Priority | Action |
|----------|--------|
| High | Include interface contracts, type signatures, and constraints |
| High | Include the specific spec section relevant to the current task |
| Medium | Include reference implementations of similar patterns |
| Low | Include full file contents when only a function signature is needed |
| Avoid | Include unrelated code, verbose documentation, or prior conversation history about different tasks |

The general rule: **include what constrains the output, exclude what merely informs
the human** [1][2].

### Layered Context

Context arrives at the model from multiple sources, each with different scope and
persistence:

| Layer | Scope | Loaded | Example |
|-------|-------|--------|---------|
| **Global** | Entire project | Automatically, every session | `CLAUDE.md`, `.cursorrules`, `copilot-instructions.md` |
| **Project** | Feature or module | On demand, per task | Spec files, design documents, interface contracts |
| **Session** | Current conversation | Accumulated during session | Prior messages, tool outputs, error logs |
| **Task** | Current generation request | Explicit in the current prompt | "Implement the `/api/tasks` endpoint per section 3.2 of the spec" |

Global context should contain constraints and conventions that apply everywhere.
Project context should contain the spec and design artifacts. Session context
accumulates naturally. Task context is the most targeted and highest-value layer --
it tells the model exactly what to produce right now [1][3].

---

## The Spec-to-Prompt Pipeline

### Start with Architecture, Add Detail Progressively

The spec-to-prompt pipeline follows the SDD lifecycle: high-level architecture first
(from the Define and Decompose phases), then constraints (from the Constrain phase),
then the specific generation task.

| Step | What to Include | Why |
|------|----------------|-----|
| 1. Architecture context | System overview, component boundaries, data flow | Prevents the AI from making structural decisions that conflict with the design |
| 2. Interface contracts | API schemas, type signatures, function signatures | Constrains inputs and outputs so generated code is compatible with adjacent components |
| 3. Constraints | Coding standards, security rules, "Never Do" list | Bounds the solution space |
| 4. Task specification | The specific unit of work to generate | Focuses generation on a single, bounded deliverable |
| 5. Reference code | Existing patterns to follow | Anchors the AI to established conventions in the codebase |

### Reference Anchoring

Pointing the AI to existing code that demonstrates the desired pattern is one of the
highest-leverage context engineering techniques. Rather than describing a pattern in
prose, show a concrete example [2][3].

Effective reference anchoring:

- "Follow the pattern in `src/api/users.ts` for error handling and response formatting."
- "Use the same validation approach as `src/schemas/task.schema.ts`."
- "Match the test structure in `tests/api/users.test.ts`."

This works because LLMs are better at pattern-matching from examples than at
interpreting abstract descriptions [1].

### Single-Pass vs Multi-Pass Generation

| Approach | When to Use | Tradeoffs |
|----------|------------|-----------|
| **Single-pass** | Small, well-constrained tasks (<200 LOC). Clear interfaces. Spec fully defines the output. | Faster. Less opportunity for context drift. Requires a complete, unambiguous spec. |
| **Multi-pass** | Complex features. Multiple interdependent files. Tasks that require intermediate validation. | More control. Each pass can build on validated output from the prior pass. Higher total token cost. Risk of context drift between passes. |

The decision rule from Osmani: if the task requires more context than fits comfortably
in a single prompt alongside the spec and constraints, split it into multiple passes.
Each pass should produce a verifiable intermediate artifact [2].

---

## Tool-Specific Context Mechanisms

| Mechanism | Claude Code | Cursor | GitHub Copilot | Kiro |
|-----------|-------------|--------|----------------|------|
| **System instructions** | `CLAUDE.md` [3] | `.cursorrules` [4] | `copilot-instructions.md` [5] | Built-in requirements phase |
| **Plan mode** | Shift+Tab (think before acting) | Composer (long-context generation) | -- | Requirements + design phases |
| **File context** | `@file`, automatic indexing, `/read` | `@file`, `@codebase` | `@workspace`, `@file` | Automatic project indexing |
| **Memory / persistence** | Memory files in `.claude/` [3] | `.cursor/` project state | -- | Specs persist across sessions |
| **Skills / commands** | Custom slash commands [3] | -- | spec-kit CLI [6] | Built-in spec workflow |
| **Context protocol** | MCP servers for external context | MCP servers | MCP servers (preview) | -- |

### Key Differences

**Claude Code** provides the most explicit context control. `CLAUDE.md` files are
hierarchical (root + subdirectory), memory files persist across sessions, and custom
slash commands can encode multi-step workflows. The `/read` command and `@file` syntax
allow precise file inclusion [3].

**Cursor** relies on `.cursorrules` for project conventions and `@codebase` for
broad context retrieval. Its composer mode supports long-context generation across
multiple files. Context is managed implicitly through the IDE's indexing [4].

**GitHub Copilot** uses `copilot-instructions.md` in the `.github/` directory for
repo-level instructions. The `@workspace` agent indexes the full repository. GitHub's
spec-kit provides a CLI for managing spec documents as structured context [5][6].

**Kiro** integrates the SDD lifecycle directly into the IDE. Requirements, design,
and constraints are captured in a structured workflow that automatically feeds into
generation. Context management is built into the tool rather than managed by the
developer [7].

---

## Iterative Refinement

### When to Refine the Spec vs Refine the Prompt

| Signal | Action | Rationale |
|--------|--------|-----------|
| AI produces incorrect architecture | Refine the **spec** (Phase 2: Decompose) | Structural issues originate in design, not prompting |
| AI uses prohibited pattern | Refine the **spec** (Phase 3: Constrain) | Missing or unclear constraint |
| AI misunderstands a single requirement | Refine the **prompt** (add clarification) | Localized ambiguity, not systemic |
| Same issue recurs across sessions | Refine the **spec** or **global context** | If a fix does not persist, it belongs in a persistent artifact |
| Output is correct but uses wrong style | Refine **global context** (CLAUDE.md / .cursorrules) | Style is a project-wide concern |

**Rule: if you are fixing the same issue twice, the spec is incomplete.** A recurring
fix should be encoded as a constraint, added to the spec, or captured in the global
context file. One-time prompt adjustments are acceptable; repeated prompt adjustments
indicate a missing specification [2].

### Handling Non-Determinism

LLMs are inherently non-deterministic. The same prompt can produce different outputs
on different runs. Constraints reduce variance by narrowing the solution space:

| Technique | Effect on Variance |
|-----------|--------------------|
| Provide explicit type signatures | Constrains function interfaces -- reduces structural variance |
| Include reference implementation | Anchors style and patterns -- reduces stylistic variance |
| Specify test cases in the spec | Constrains behavior -- reduces functional variance |
| Use "Never Do" lists | Eliminates prohibited patterns -- reduces defect variance |
| Pin framework versions | Eliminates API surface variance |

The goal is not to eliminate non-determinism (which is impossible) but to constrain
it so that all valid outputs are acceptable [2][8].

---

## Context Window Budget

Approximate token costs for common context elements. These are estimates based on
typical project configurations; actual sizes vary.

| Context Layer | Typical Size | Priority | Notes |
|---------------|-------------|----------|-------|
| System instructions (`CLAUDE.md` / `.cursorrules`) | 500 -- 2,000 tokens | Always loaded | Keep concise. Every token here competes with task context. |
| Project constraints (coding standards, security rules) | 1,000 -- 3,000 tokens | Always loaded | Should be stable across sessions. |
| Spec document (current task) | 2,000 -- 8,000 tokens | Per-task | The primary payload. Scales with task complexity. |
| Reference code (existing patterns) | 2,000 -- 10,000 tokens | As needed | Include only the relevant functions, not entire files. |
| Interface contracts (types, schemas) | 500 -- 2,000 tokens | Per-task | High value per token. Always include when available. |
| Conversation history | Variable | Auto-managed | Older turns are compressed or dropped by the tool. |
| Tool outputs (file reads, search results) | Variable | On demand | Can dominate the context window if not managed. |

### Budget Management Principles

1. **Constraints and types are the highest-value tokens.** They constrain every line
   of generated code. Prioritize them over verbose descriptions.
2. **Reference code beats prose.** A 50-line example communicates more than a 500-word
   description of the same pattern.
3. **Trim conversation history aggressively.** Prior turns about different tasks add
   noise. Start new sessions for unrelated tasks.
4. **Measure, do not guess.** Most tools provide token usage information. Use it to
   understand where the context budget is going [1][3].

---

## References

1. Fowler, M. "Context Engineering for Coding Agents." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html

2. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

3. Anthropic. "Claude Code Documentation." 2025-2026.
   https://docs.anthropic.com/en/docs/claude-code

4. Cursor. "Rules for AI." 2025-2026.
   https://docs.cursor.com/context/rules-for-ai

5. GitHub. "Copilot Instructions." 2025-2026.
   https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot

6. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

7. Fowler, M. "Understanding SDD -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

8. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

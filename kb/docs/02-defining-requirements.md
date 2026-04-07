# Phase 1: Defining Requirements for AI Agents

> Last verified: 2026-04-04

---

## Principle

Write requirements that answer "what" and "why" clearly enough that an AI agent
can derive "how" without ambiguity. A well-written spec eliminates the single
largest source of AI coding defects: underspecified intent [1][2].

---

## 1. The Anatomy of an Effective Requirement

Every requirement fed to an AI agent should contain six sections. Omitting any
one of them forces the agent to guess -- and guessing is the mechanism by which
AI-generated code diverges from developer intent [1].

| Section | Purpose | What Happens When Missing |
|---|---|---|
| **Goal statement** (1-2 sentences) | States the user-visible outcome | Agent optimizes for plausible code instead of correct code |
| **User context** | Who uses this, what workflow it fits | Agent makes wrong UX assumptions |
| **Functional requirements** | Enumerated behaviors the system must exhibit | Agent invents behaviors or omits edge cases |
| **Non-functional requirements** | Performance budgets, security constraints, accessibility standards | Agent ignores runtime characteristics entirely |
| **Success criteria** (testable) | Observable, measurable conditions for "done" | No objective way to verify output |
| **Out of scope** (explicit boundaries) | What this feature deliberately does NOT do | Agent gold-plates or implements adjacent features |

The goal statement is the single most important element. Osmani's research shows
that a clear, concise goal statement reduces spec-to-code divergence because it
gives the LLM a consistent objective function to optimize against throughout
generation [1].

Success criteria must be **testable** -- written as assertions that can be
evaluated programmatically or through deterministic manual steps. "The page
should load quickly" is not a success criterion. "First Contentful Paint < 1.5s
on a 4G connection" is.

---

## 2. The PRD + SRS Hybrid

Traditional software development separates the **Product Requirements Document**
(PRD) from the **Software Requirements Specification** (SRS). The PRD captures
the "why" -- business context, user needs, success metrics. The SRS captures the
"what" -- functional behavior, interfaces, data models, constraints.

For AI-assisted development, Osmani recommends **merging the two** [1]. The
reasoning: an AI agent needs both the intent (to make correct architectural
decisions) and the technical specifics (to generate correct implementation) in a
single context window. Splitting them across documents forces the developer to
either load both into context (consuming tokens) or lose one dimension of
guidance.

**Structure of the hybrid:**

1. **Start like a PRD.** Open with the problem statement, user persona, and
   business justification. This anchors the agent's understanding of *why* the
   feature exists.
2. **Expand like an SRS.** Follow with functional requirements, interface
   contracts, data models, error handling expectations, and performance budgets.
   This gives the agent the precision it needs to generate correct code.
3. **Close with verification.** End with success criteria and test scenarios that
   map directly to the requirements above. This creates a closed loop: the agent
   can self-check its output against the spec.

The hybrid approach is consistent with GitHub's spec-kit framework, which
structures specs as a progression from intent to implementation to verification
[3].

---

## 3. Context Window Awareness

A spec is useless if it exceeds the practical context limits of the agent's
working memory. While modern LLMs support large context windows (100K-1M+
tokens), effective utilization degrades with length. Research on long-context
retrieval shows that information in the middle of long contexts is recalled less
reliably than information at the beginning or end [4].

### Token Budget Rules of Thumb

| Spec Level | Target Size | Contains |
|---|---|---|
| **High-level overview** | < 2,000 tokens | Goal, user context, key requirements, scope boundaries |
| **Detailed spec** | < 8,000 tokens | Full functional requirements, interface contracts, success criteria, examples |
| **Implementation guide** | < 16,000 tokens | Detailed spec + code examples, API schemas, data models |

### Techniques for Staying Within Budget

**Layered specs.** Structure the specification in layers of increasing detail.
The agent receives the high-level layer by default; detailed layers are loaded
on demand for specific components. This mirrors how Kiro structures its spec
phases -- requirements, design, and tasks are separate artifacts that compose
into a full spec [5].

**Reference links.** Instead of embedding large external specifications (e.g., a
full OpenAPI schema), provide a summary and a file path or URL. The agent can
fetch the full content when needed, keeping the primary context lean.

**Progressive disclosure.** Front-load the goal and constraints. Place examples
and edge cases later in the document. This exploits the primacy bias in LLM
attention: the most important information gets the strongest signal.

---

## 4. Writing Techniques

### Start High, Expand with AI

The most efficient workflow for spec authoring uses the AI agent itself:

1. **Human writes a brief** (3-5 sentences). Captures the goal, primary user,
   and key constraints.
2. **AI expands the brief** using Plan Mode (Claude Code) or equivalent
   planning features. The agent asks clarifying questions, identifies edge
   cases, and produces a structured draft spec [1].
3. **Human reviews and edits.** Corrects assumptions, adds domain knowledge the
   AI lacks, removes hallucinated requirements.
4. **Iterate once.** One round of AI expansion followed by human review
   typically produces a spec of sufficient quality for generation.

This approach leverages AI's strength (exhaustive enumeration of edge cases) and
human's strength (domain knowledge and intent validation).

### Include Examples

Concrete examples reduce ambiguity more effectively than abstract descriptions.
Every functional requirement should include at least one example [1][3].

| Example Type | When to Use | Format |
|---|---|---|
| **Input/output pairs** | Data transformation, parsing, calculation | `Input: "2024-01-15" -> Output: "January 15, 2024"` |
| **API request/response** | REST endpoints, GraphQL queries | Full HTTP request and response with headers and body |
| **State transitions** | Stateful workflows, UI interactions | Before/after snapshots with triggering event |
| **UI mockups** | Visual features, layout requirements | ASCII art, wireframe reference, or screenshot |
| **Error scenarios** | Validation, failure modes | Invalid input and expected error response |

### Explicit Over Implicit

AI agents interpret ambiguous language literally, creatively, or inconsistently
depending on context. Replace every implicit assumption with an explicit
statement [1][2].

| Ambiguous Phrasing | Problem | Specific Phrasing |
|---|---|---|
| "Handle errors appropriately" | "Appropriately" is undefined; agent may swallow exceptions, log them, or throw -- unpredictably | "Return HTTP 400 with `{error: string, code: string}` for validation failures; return HTTP 500 with the same shape for unexpected errors; log all errors to stdout in JSON format" |
| "Support large files" | "Large" is undefined | "Support files up to 500 MB; reject files over 500 MB with a 413 status code and a message stating the limit" |
| "The page should be fast" | No measurable target | "Largest Contentful Paint < 2.5s on a median mobile device (Moto G Power) over a 4G connection" |
| "Use standard authentication" | Which standard? OAuth2? API keys? Session cookies? | "Use OAuth 2.0 Authorization Code flow with PKCE; accept Bearer tokens in the Authorization header; tokens expire after 3600 seconds" |
| "Make it responsive" | Responsive to what breakpoints? | "Three breakpoints: mobile (< 768px, single column), tablet (768-1024px, two columns), desktop (> 1024px, three columns with 240px sidebar)" |

---

## 5. Template: Feature Spec

The following template covers the sections identified in Section 1. Annotations
in square brackets explain the purpose of each field.

```markdown
# Feature: [Feature Name]

## Goal
[1-2 sentences. What user-visible outcome does this feature produce?
This is the single most important section -- it anchors the entire generation.]

## User Context
[Who uses this feature? What workflow does it fit into? What are they trying
to accomplish? Include the user's technical level if relevant to UI/UX
decisions.]

## Functional Requirements
[Enumerated list of behaviors. Each requirement should be independently
testable. Use RFC 2119 language (MUST, SHOULD, MAY) for clarity.]

1. The system MUST ...
2. The system MUST ...
3. The system SHOULD ...
4. The system MAY ...

### Examples
[At least one input/output example per non-trivial requirement.]

**Example 1:** [description]
- Input: ...
- Expected output: ...

## Non-Functional Requirements

### Performance
- [Measurable target, e.g., "p95 response time < 200ms"]

### Security
- [Authentication/authorization requirements]
- [Data handling constraints, e.g., "PII must not be logged"]

### Accessibility
- [WCAG level, keyboard navigation, screen reader requirements]

## Success Criteria
[Testable assertions. Each criterion maps to one or more functional
requirements above.]

1. [ ] [Criterion 1 -- maps to FR-1]
2. [ ] [Criterion 2 -- maps to FR-2]
3. [ ] [Criterion 3 -- maps to NFR-Performance]

## Out of Scope
[Explicit boundaries. What this feature deliberately does NOT do.
This prevents the agent from gold-plating.]

- This feature does NOT ...
- This feature does NOT ...

## Open Questions
[Unresolved decisions. Mark these with [NEEDS CLARIFICATION] so the agent
can flag them rather than guess.]

- [NEEDS CLARIFICATION] Should the API support pagination or return all
  results?
```

---

## 6. Template: Bug Fix Spec

Bug fixes require a shorter spec focused on reproduction and verification rather
than requirements discovery.

```markdown
# Bug Fix: [Bug Title]

## Bug Description
[1-2 sentences. What is broken? Include the affected component/module.]

## Expected Behavior
[What should happen under the conditions described below.]

## Actual Behavior
[What actually happens. Include error messages, incorrect outputs, or
observable symptoms.]

## Reproduction Steps
[Numbered steps to reliably trigger the bug. Include environment details
if relevant (OS, runtime version, configuration).]

1. ...
2. ...
3. Observe: [symptom]

## Fix Constraints
[Boundaries for the fix. What approaches are acceptable or prohibited.]

- The fix MUST NOT change the public API surface.
- The fix MUST NOT introduce new dependencies.
- The fix SHOULD be backward-compatible with [version].

## Verification Criteria
[How to confirm the fix is correct and complete.]

1. [ ] Reproduction steps no longer produce the bug.
2. [ ] Existing test suite passes without modification.
3. [ ] [New test case] covers the specific scenario that triggered the bug.
```

---

## References

1. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

4. Liu, N. F., et al. "Lost in the Middle: How Language Models Use Long Contexts." arXiv:2307.03172. 2023.
   https://arxiv.org/abs/2307.03172

5. Fowler, M. "Understanding SDD -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

6. GitHub spec-kit. "spec-driven.md." 2026.
   https://github.com/github/spec-kit

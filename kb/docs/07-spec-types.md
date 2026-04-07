# Spec Types and When to Use Each

> Last verified: 2026-04-04

---

## Principle

Different development scenarios call for different spec types. Using the wrong type
wastes effort; using no type leads to vibe coding. A well-chosen spec type focuses
the AI agent on the right information at the right level of detail, reducing context
waste and improving first-pass generation quality.

---

## Taxonomy

| Spec Type | Purpose | When to Use | Typical Size |
|---|---|---|---|
| **Feature Spec** | New capability end-to-end | Greenfield features, >100 LOC expected | 500-2000 words |
| **Interface Spec** | API/component contracts | Multi-component systems, team boundaries | 200-800 words |
| **Constraint Spec** | Rules and boundaries | Project-wide standards, cross-cutting concerns | 100-500 words |
| **Test Spec** | Expected behaviors and scenarios | Before implementation (TDD), acceptance criteria | 200-1000 words |
| **Delta Spec** | Changes to existing code | Brownfield modifications, refactors | 100-500 words |
| **Bug Fix Spec** | Targeted defect resolution | Known issues with clear reproduction steps | 50-200 words |

The size ranges reflect the sweet spot for AI context efficiency. Specs above 2000
words risk exceeding the effective attention window for a single generation task.
Specs below 50 words typically lack the precision needed to constrain generation [1].

---

## Detailed Breakdown

### Feature Spec

**Definition.** A complete specification for a new capability, covering what the
feature does, who it serves, how it integrates with existing systems, and how
success is measured.

**Key sections it must contain:**

- User story or problem statement
- Acceptance criteria (testable, specific)
- Data model and state management
- Error handling and edge cases
- Constraints (technology, performance, security)
- Out of scope (explicit exclusions)

**Common mistakes:**

- Including implementation details (pseudocode, class hierarchies) instead of
  behavioral requirements.
- Writing acceptance criteria as vague aspirations ("should be fast") instead of
  measurable thresholds ("API response under 200ms at p95").
- Omitting the "out of scope" section, causing the AI to gold-plate features.

**When NOT to use it:** Small changes to existing code (use Delta Spec), bug fixes
(use Bug Fix Spec), or when the work is purely contractual between components (use
Interface Spec).

---

### Interface Spec

**Definition.** A contract definition for how components, services, or modules
communicate -- covering endpoints, payloads, error codes, and invariants that both
sides must respect.

**Key sections it must contain:**

- Endpoint or function signatures
- Request/response schemas (with types)
- Error codes and error response format
- Authentication and authorization requirements
- Rate limits and SLAs (if applicable)
- Versioning strategy

**Common mistakes:**

- Specifying only the happy path and omitting error contracts.
- Mixing behavioral requirements (what the endpoint does) with interface
  requirements (what the contract looks like).
- Failing to version the interface, leading to breaking changes downstream.

**When NOT to use it:** Single-component work with no external consumers (use
Feature Spec), or when the interface already exists and you are modifying behavior
behind it (use Delta Spec).

---

### Constraint Spec

**Definition.** A set of rules, prohibitions, and boundaries that apply across
multiple features or the entire project. Constraint specs do not describe what to
build -- they describe how everything must be built.

**Key sections it must contain:**

- Coding standards (naming, structure, patterns)
- Technology restrictions (allowed/prohibited libraries)
- Security requirements (input validation, auth patterns)
- Performance budgets (response times, bundle sizes, memory)
- The "Never Do" list (explicit prohibitions)

**Common mistakes:**

- Writing constraints so broadly that they are unenforceable ("code should be
  clean").
- Failing to encode constraints in tooling (linters, CI checks) alongside the spec.
- Duplicating constraints across multiple specs instead of centralizing them.

**When NOT to use it:** One-off requirements that apply to a single feature (embed
constraints in the Feature Spec instead). Constraint specs are for cross-cutting
concerns, not feature-specific rules.

---

### Test Spec

**Definition.** A structured enumeration of expected behaviors, scenarios, and
edge cases that define what "correct" means -- written before implementation begins.

**Key sections it must contain:**

- Scenario descriptions (Given/When/Then or equivalent)
- Input-output pairs for key paths
- Edge cases and boundary conditions
- Error scenarios and expected error behavior
- Performance assertions (if applicable)
- Test data requirements

**Common mistakes:**

- Writing tests that mirror implementation rather than requirements (testing how
  instead of what).
- Omitting negative test cases (what should NOT happen).
- Specifying test framework details instead of behaviors -- the spec should be
  framework-agnostic where possible.

**When NOT to use it:** When tests are trivially derivable from a Feature Spec's
acceptance criteria (avoid redundant specification). When the work is a refactor
with no behavioral changes (existing tests already cover correctness).

---

### Delta Spec

**Definition.** A targeted specification for changes to existing code, describing
what currently exists, what must change, and what must remain unchanged.

**Key sections it must contain:**

- Current behavior (what exists today)
- Desired behavior (what must change)
- Preservation requirements (what must NOT change)
- Affected components and files
- Migration or backward-compatibility requirements

**Common mistakes:**

- Describing only the desired end state without specifying what currently exists,
  forcing the AI to guess the baseline.
- Omitting preservation requirements, causing the AI to refactor adjacent code
  unnecessarily.
- Failing to reference the existing codebase structure, leading to generated code
  that does not integrate.

**When NOT to use it:** Greenfield features with no existing code to modify (use
Feature Spec). When the change is a defect fix with a clear reproduction (use Bug
Fix Spec).

---

### Bug Fix Spec

**Definition.** A minimal specification for resolving a known defect, containing
the reproduction steps, expected behavior, actual behavior, and acceptance criteria
for the fix.

**Key sections it must contain:**

- Reproduction steps (exact sequence)
- Expected behavior
- Actual behavior (with error messages, stack traces, or screenshots)
- Root cause analysis (if known)
- Fix acceptance criteria

**Common mistakes:**

- Omitting reproduction steps, making it impossible to verify the fix.
- Expanding scope beyond the defect ("while we're in there, also refactor...").
- Not specifying regression criteria -- what must still work after the fix.

**When NOT to use it:** When the "bug" is actually a missing feature (use Feature
Spec or Delta Spec). When the root cause is unclear and requires exploratory
investigation before specification.

---

## Combining Spec Types

Real implementation tasks rarely map to a single spec type. Most non-trivial work
requires composing multiple specs, each handling a different concern.

### Example: New API Endpoint for User Search

A new search endpoint in an existing application requires four spec types working
together:

| Spec Type | Covers | Example Content |
|---|---|---|
| **Feature Spec** | What the search does, who uses it, acceptance criteria | "Users can search by name, email, or role. Results are paginated. Maximum 100 results per page." |
| **Interface Spec** | The API contract | "GET /api/users/search?q={query}&page={n}. Returns 200 with `{users: [], total: number, page: number}`. Returns 400 for missing query parameter." |
| **Constraint Spec** | Performance and security boundaries | "Search response under 500ms at p95 with 1M user records. Query input sanitized against SQL injection. Rate limited to 30 requests/minute per user." |
| **Test Spec** | Verification scenarios | "Given 50 users with 'Smith' in name, when searching for 'Smith' with page=1, then return first 20 results sorted by relevance with total=50." |

The composition order matters. Feature Spec establishes scope, Interface Spec
defines the contract, Constraint Spec bounds the solution space, and Test Spec
provides verification criteria. Each spec type references the others where
dependencies exist.

### Composition Principles

1. **Each spec type handles one concern.** Do not embed interface definitions inside
   a Feature Spec or performance budgets inside a Test Spec.
2. **Cross-reference, do not duplicate.** If the Interface Spec defines the response
   schema, the Test Spec references it rather than restating it.
3. **Constraint Specs are shared.** A single Constraint Spec often applies to
   multiple Feature and Interface Specs. Centralize it.
4. **Feed specs to the AI in dependency order.** The AI needs the Feature Spec and
   Constraint Spec in context before generating code. The Test Spec may be fed
   separately for TDD workflows.

---

## Decision Matrix

| Scenario | Primary Spec | Supporting Specs |
|---|---|---|
| Add search to existing app | Delta Spec | Interface Spec, Constraint Spec |
| New microservice from scratch | Feature Spec | Interface Spec, Constraint Spec, Test Spec |
| Fix login bug | Bug Fix Spec | -- |
| Refactor auth module | Delta Spec | Constraint Spec |
| New third-party integration | Interface Spec | Feature Spec, Test Spec |
| Performance optimization | Delta Spec | Constraint Spec, Test Spec |
| New CLI tool | Feature Spec | Interface Spec, Test Spec |
| Database schema migration | Delta Spec | Constraint Spec |
| Add test coverage to existing code | Test Spec | -- |
| New shared component library | Feature Spec | Interface Spec, Constraint Spec, Test Spec |
| Hotfix for production outage | Bug Fix Spec | -- |
| Replace dependency (e.g., ORM swap) | Delta Spec | Constraint Spec, Test Spec |

The primary spec drives the AI generation task. Supporting specs provide context
and constraints that bound the generation but are not the main input.

---

## References

1. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. GitHub. "OpenSpec: Delta Specs for Brownfield Development." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

4. GitHub. "spec-kit: Spec-Driven Development Toolkit." 2026.
   https://github.com/nickvidal/spec-kit

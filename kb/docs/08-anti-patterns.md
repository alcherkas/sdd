# SDD Anti-Patterns

> Last verified: 2026-04-04

---

## Principle

Knowing what fails is as valuable as knowing what works. These anti-patterns have
been observed across teams adopting spec-driven development. Each represents a
recurring failure mode that degrades the quality of AI-generated output, wastes
developer time, or silently undermines the SDD lifecycle.

Severity levels: **Critical** (breaks the SDD contract entirely), **High**
(significantly degrades output quality), **Medium** (causes inefficiency or
gradual drift).

---

## 1. The Spec Novel

**Severity: Critical**

**Problem.** Writing specifications that exceed 5000 words, attempting to capture
every detail of a complex feature in a single document. These specs exceed the
effective attention window of AI models, causing the agent to lose track of
requirements stated earlier in the document.

**Symptom.** The AI generates code that correctly implements the last third of the
spec while ignoring or contradicting requirements from the first third. Later
sections overwrite earlier ones in the model's attention. Developers report that
"it got the details right but missed the big picture."

**Fix.** Layer specs by decomposing them into focused documents, each under 2000
tokens. Use a Feature Spec for the high-level capability, separate Interface Specs
for contracts, and separate Constraint Specs for boundaries. Feed them to the AI in
dependency order rather than as a single monolith.

---

## 2. Spec-and-Forget

**Severity: High**

**Problem.** Writing the spec once before implementation and never updating it as
the system evolves. The spec becomes a historical artifact rather than a living
document, and subsequent AI generation sessions use outdated context.

**Symptom.** Spec states one interface contract; code implements a different one
that evolved during development. New developers or AI sessions that reference the
spec produce code incompatible with the actual system. The spec is technically
present but functionally useless.

**Fix.** Treat specs as living documents. Update the spec during verification
(Phase 5) whenever a gap is found between spec and implementation. Version specs
alongside code in the same repository. Include spec review as a required step in
pull request checklists. If the code changed, the spec must change to match -- or
the code must be reverted to match the spec [1].

---

## 3. Vibe Spec

**Severity: High**

**Problem.** Writing specifications in vague, aspirational language that sounds
directional but provides no concrete criteria for the AI to implement against.

**Symptom.** Spec contains phrases like "make it user-friendly," "ensure good
performance," or "follow best practices" with no measurable definitions. The AI
interprets these subjectively, producing output that technically satisfies the
words but not the intent. Each generation session may interpret the vague language
differently, producing inconsistent results.

**Fix.** Every requirement must be testable. Replace "make it fast" with "API
response under 200ms at p95." Replace "user-friendly" with specific UX criteria:
"form completes in under 3 clicks, error messages appear inline within 100ms."
If a requirement cannot be stated in testable terms, it is not yet a requirement --
it is a wish [2].

---

## 4. Implementation Spec

**Severity: Medium**

**Problem.** Dictating exact code structure, class hierarchies, or algorithmic
approaches in the spec instead of specifying desired behavior and constraints. The
spec reads like pseudocode rather than requirements.

**Symptom.** The spec prescribes "create a UserService class with a
findByEmail method that queries the users table using a SELECT statement." The AI
follows the prescription literally, even when a better approach exists within the
stated constraints. The developer has replaced AI judgment with their own
implementation assumptions, losing the leverage that AI generation provides.

**Fix.** Specify the what and why, not the how. State the required behavior ("given
an email address, return the matching user profile or a 404 error"), the constraints
("response under 50ms, SQL injection safe"), and the interface contract ("GET
/users?email={email}"). Let the AI determine the implementation within those bounds.
Reserve implementation directives for cases where a specific approach is genuinely
required (e.g., regulatory compliance mandating a particular encryption algorithm).

---

## 5. Skipping Verification

**Severity: Critical**

**Problem.** Trusting AI-generated code without systematically checking it against
the spec. The developer glances at the output, confirms it compiles and runs, and
ships it.

**Symptom.** Drift between intent and implementation accumulates silently. The code
passes superficial inspection but fails edge cases, violates constraints, or
implements a subtly different interface than specified. Bugs surface weeks later in
production, long after the generation context is lost.

**Fix.** Verification is mandatory and non-negotiable. Use the verification
approaches defined in Phase 5: automated tests derived from the spec, spec-compliance
checks, LLM-as-judge patterns for independent review, and human review for
architectural coherence. No generated code merges without passing verification
against the spec it was generated from [1][3].

---

## 6. One Giant Prompt

**Severity: High**

**Problem.** Dumping the entire project specification into a single generation
session and expecting coherent output across all components simultaneously.

**Symptom.** The AI produces output that is internally inconsistent: module A calls
module B with one interface, while module B implements a different one. Requirements
from earlier in the prompt are forgotten or contradicted by later generation.
The output looks complete but does not compose into a working system.

**Fix.** Decompose into focused generation tasks, each with a scoped spec. Generate
one component at a time, verify it, then proceed to the next. Use Interface Specs
to define contracts between components before generating either side. The
decomposition from Phase 2 of the SDD lifecycle exists specifically to prevent this
pattern [2].

---

## 7. Copy-Paste Architecture

**Severity: Medium**

**Problem.** The AI replicates common patterns from its training data instead of
matching the architecture specified in your constraints. It produces working code
that follows generic conventions rather than your project's specific patterns.

**Symptom.** Generated code uses a repository pattern when your project uses direct
database access. Generated React components use class-based patterns when your
project uses functional components exclusively. The code works in isolation but
clashes with existing architecture. Integration requires significant rework.

**Fix.** Write explicit Constraint Specs that define your architectural patterns.
Include concrete examples of existing code that follows the pattern. Reference
existing files in the codebase that demonstrate the expected approach. The AI cannot
infer your architecture from a vague prompt -- it will default to the most common
pattern in its training data. Explicit constraints override training defaults [2].

---

## 8. Spec Without Tests

**Severity: High**

**Problem.** Writing requirements with no verification criteria. The spec describes
what to build but provides no definition of how to confirm it was built correctly.

**Symptom.** Acceptance is based on "it looks right" or "it seems to work." The
developer manually clicks through the feature, confirms it does something plausible,
and considers it done. Regression is discovered only when users report bugs. There
is no automated way to verify that the AI's output matches the spec.

**Fix.** Include test criteria in every spec. Feature Specs must have acceptance
scenarios. Interface Specs must have request-response examples that can be converted
to integration tests. Constraint Specs must reference linter rules or CI checks
that enforce them automatically. A spec without test criteria is a spec that cannot
be verified, and unverifiable specs are the gateway to vibe coding [1][3].

---

## Summary

| Anti-Pattern | Severity | Root Cause | Quick Fix |
|---|---|---|---|
| **The Spec Novel** | Critical | Attempting to capture everything in one document | Layer specs, keep each under 2K tokens |
| **Spec-and-Forget** | High | Treating specs as write-once artifacts | Update specs during verification; version with code |
| **Vibe Spec** | High | Vague language substituting for concrete requirements | Make every requirement testable and measurable |
| **Implementation Spec** | Medium | Dictating how instead of what | Specify behavior and constraints, not code structure |
| **Skipping Verification** | Critical | Trusting AI output without systematic checks | Mandatory verification against spec before merge |
| **One Giant Prompt** | High | No decomposition before generation | Decompose into focused tasks with scoped specs |
| **Copy-Paste Architecture** | Medium | Missing architectural constraints | Explicit Constraint Specs with code examples |
| **Spec Without Tests** | High | No verification criteria in requirements | Include test scenarios in every spec |

The two critical anti-patterns -- The Spec Novel and Skipping Verification -- break
the SDD contract entirely. The Spec Novel breaks the input side (the AI cannot
process the spec effectively), while Skipping Verification breaks the output side
(generated code is not validated against the spec). Eliminating these two patterns
alone produces the largest improvement in SDD adoption outcomes.

---

## References

1. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

2. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

3. CGI. "Spec-Driven Development for AI-Assisted Software Engineering."
   https://www.cgi.com/en/article/technology-operations/spec-driven-development

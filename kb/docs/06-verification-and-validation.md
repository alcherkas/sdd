# Phase 5: Verification and Validation

> **Last verified: 2026-04-04**

---

## Principle

Every generation must be verified against the spec. Verification is not optional --
it is the mechanism that makes SDD work. Without it, you have vibe coding with extra
steps.

---

## Verification vs Validation

These are distinct activities. Both are required.

| Dimension | Verification | Validation |
|---|---|---|
| **Question** | "Did we build it right?" | "Did we build the right thing?" |
| **Compares** | Code against spec | Spec against user need |
| **Automation** | Highly automatable | Requires human judgment |
| **Failure mode** | Code diverges from spec | Spec diverges from intent |
| **Fix target** | Regenerate code (or refine constraints) | Revise requirements (Phase 1) |

Verification without validation catches implementation bugs but misses
specification bugs. Validation without verification catches intent mismatches but
misses generation errors. Neither substitutes for the other.

---

## Verification Approaches

| Approach | What It Checks | Automation Level | When to Use |
|---|---|---|---|
| **Automated Testing (TDD Integration)** | Acceptance scenarios from spec execute correctly | Full | Always -- tests ARE the spec in executable form |
| **Spec-Compliance Checks** | Each spec requirement has a corresponding implementation | Partial (AI-assisted or manual) | Every generation cycle |
| **Self-Verification Prompts** | AI reviews its own output against the spec | Full | First pass only -- not sufficient alone |
| **LLM-as-Judge Patterns** | Separate AI session evaluates whether code satisfies the spec | Full | When self-evaluation bias is a concern |
| **Human Review** | Architectural coherence, security-critical paths, novel patterns | Manual | Where AI judgment is weakest |

### Automated Testing (TDD Integration)

Acceptance scenarios defined in Phase 1 become executable tests, written before code
generation begins. The tests are the spec in executable form. When the AI generates
code in Phase 4, those tests are the first verification gate.

This is standard TDD -- the difference in SDD is that the spec provides a structured
source for test derivation rather than relying on developer intuition alone.

### Spec-Compliance Checks

Systematic comparison of generated code against each requirement in the spec. This
goes beyond test pass/fail to verify structural compliance: Does the code use the
specified interfaces? Does it respect the constraint boundaries? Does every
requirement have a corresponding implementation?

Can be manual (checklist-based) or AI-assisted (prompt the AI to enumerate which
requirements are and are not satisfied).

### Self-Verification Prompts

Ask the generating AI to review its own output against the spec. Useful as a fast
first pass -- the AI can catch obvious omissions and misinterpretations. However,
self-evaluation suffers from the same biases that caused the error in the first
place. Never rely on this alone.

### LLM-as-Judge Patterns

Use a separate AI session -- ideally a different model or at minimum a fresh
context -- to evaluate whether generated code satisfies the spec. This reduces
self-evaluation bias by introducing an independent reviewer that has not seen the
generation process.

The judge receives only the spec and the generated code, then produces a structured
compliance report. Disagreements between generator and judge are escalated to human
review.

### Human Review

Required for:

- **Architectural coherence** -- does the generated code fit the broader system?
- **Security-critical paths** -- authentication, authorization, data handling
- **Novel patterns** -- code that does something the AI has not been trained on
- **Constraint validation** -- are the constraints themselves correct?

Focus human attention where AI judgment is weakest. Do not waste human review cycles
on things automated tests can catch.

---

## The Feedback Loop

When verification or validation fails, the fix goes to the spec -- not the code.

| Failure Type | Routes Back To | Action |
|---|---|---|
| Test failure | Phase 3 (Constrain) or Phase 1 (Define) | Refine constraints or clarify requirements |
| Architecture drift | Phase 2 (Decompose) | Revisit component boundaries |
| Spec ambiguity discovered | Phase 1 (Define) | Add clarity, resolve ambiguous definitions |
| Performance miss | Phase 3 (Constrain) | Tighten performance budgets |

**Key rule:** Fix the spec first, then regenerate. Patching generated code directly
breaks the SDD contract. The moment you start editing generated code to "make it
work" without updating the spec, you have abandoned the spec as the source of truth
and reintroduced the drift that SDD exists to prevent.

---

## The Harness Feedback Loop

When validation identifies gaps between spec and implementation, classify them to
strengthen the overall development harness [1]:

| Gap Type | Root Cause | Corrective Action |
|---|---|---|
| **Spec-to-implementation gap** | Validation mechanisms in the plan are insufficient | Strengthen verification steps -- add tests, tighten compliance checks, add CI gates |
| **Intent-to-specification gap** | Elicitation missed requirements or captured them ambiguously | Improve question patterns, add structured elicitation templates, require stakeholder sign-off |

The critical insight: each gap identified strengthens the harness for ALL future
implementations, not just the current one. Over time, the team accumulates a
library of known failure modes and corresponding verification mechanisms. The spec
process itself improves through use.

This is a compounding advantage. Teams that systematically classify and address gaps
produce progressively better specifications with each iteration.

---

## CI/CD Integration

Generated code must pass the same CI pipeline as human-written code. There are no
exceptions. Additionally, SDD introduces verification steps that go beyond standard
CI:

**Spec-compliance checks as CI steps.** Automated comparison of generated output
against declared spec requirements. This can be as simple as a checklist gate or as
sophisticated as an LLM-as-judge step in the pipeline.

**Drift detection at build time.** Compare the generated output against the declared
spec. If the code has been manually modified since generation (outside the
spec-regenerate cycle), flag the drift. This prevents silent divergence between spec
and implementation.

**Feature flags tied to spec completion status.** Features generated from incomplete
specs remain behind feature flags until all spec requirements are verified. This
allows continuous delivery without shipping unverified functionality.

```
Pipeline stages for SDD:
  1. Lint / format (standard)
  2. Unit tests (standard)
  3. Spec-compliance check (SDD-specific)
  4. Drift detection (SDD-specific)
  5. Integration tests (standard)
  6. Feature flag evaluation (SDD-specific)
  7. Deploy
```

---

## Metrics for Verification Quality

| Metric | What It Measures | Target | Why It Matters |
|---|---|---|---|
| **First-pass test rate** | % of spec'd tests passing on first generation | >60% | Measures spec quality -- low rates indicate ambiguous or incomplete specs |
| **Spec coverage** | % of requirements with corresponding tests | 100% for critical paths | Untested requirements are unverified requirements |
| **Rework rate** | % of generated code needing manual fixes | <30% | High rework rates indicate constraint or decomposition problems |
| **Drift incidents** | Times code diverges from spec in production | 0 for critical systems | Any drift means the SDD contract has been violated |
| **Gap classification rate** | % of failures classified into gap types | 100% | Unclassified failures do not improve the harness |
| **Harness improvement rate** | Reduction in repeat failure modes over time | Monotonically decreasing | Measures whether the team is learning from verification failures |

Track these metrics per team and per project. Trends matter more than absolute
values -- a team whose first-pass test rate improves from 40% to 70% over three
months is doing SDD well, even if they have not hit the target yet.

---

## References

1. InfoQ. "Spec-Driven Development at Enterprise Scale."
   https://www.infoq.com/articles/spec-driven-development-enterprise-scale/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

4. CGI. "Spec-Driven Development for AI-Assisted Software Engineering."
   https://www.cgi.com/en/article/technology-operations/spec-driven-development

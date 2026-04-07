# SDD and the Software Development Lifecycle

> Last verified: 2026-04-04

---

## Principle

SDD does not replace the SDLC -- it restructures each phase around specifications as the connective tissue, with continuous feedback loops replacing end-of-phase gates. Every phase produces artifacts that feed back into the specification, and every specification change propagates forward to affected phases.

---

## The Core Shift

| Aspect | Traditional SDLC | SDD |
|--------|------------------|-----|
| **Source of truth** | Code | Specification |
| **Feedback timing** | End-of-phase gates | Continuous at each step |
| **Spec lifespan** | Stale post-implementation | Living, continuously evolved |
| **Change propagation** | Manual across artifacts | Automatic flagging of affected areas |
| **Testing** | After implementation | From specification, before code |
| **Drift detection** | Post-deployment (if ever) | Build-time, deploy-time, runtime |
| **Knowledge preservation** | Lost when developers leave | Preserved in specification |

The traditional SDLC treats specifications as a transient artifact -- useful for communication, discarded after implementation. SDD treats specifications as the durable artifact that outlives any single implementation. Code is generated, tested, and regenerated. The spec persists.

---

## Phase-by-Phase Transformation

### Phase 1: Requirements --> SPECIFY

**Traditional:** Meetings produce a PRD or requirements document. It is reviewed once, approved, and handed to engineering. By the time implementation begins, the document is already drifting from the team's evolving understanding. Ambiguities are discovered during coding and resolved ad hoc.

**SDD:** Requirements are captured through iterative AI-assisted dialogue that actively probes for ambiguity. The output is a living specification with explicit `[NEEDS CLARIFICATION]` markers for unresolved questions. Research agents verify feasibility of technical requirements before the spec is considered complete. The spec undergoes structured review with gates for completeness, testability, and constraint sufficiency.

**Feedback loop:** The AI asks clarifying questions during specification, exposing gaps that meetings miss. Research agents verify that proposed approaches are feasible given the technology stack and constraints. Spec review gates enforce quality before downstream work begins.

**What flows back to the specification:** Clarification answers, feasibility findings, reviewer comments, and revised acceptance scenarios.

---

### Phase 2: Design --> PLAN

**Traditional:** Architects produce design documents -- component diagrams, interface definitions, technology choices. These documents reference the requirements but are not structurally linked to them. Design decisions are captured in meetings, Slack threads, or not at all. The design document stales within weeks of implementation start.

**SDD:** The PLAN command generates architecture directly from the specification. Every design decision traces to a specific requirement. Technology choices are validated by research agents against the constraints defined in the spec. Constitution gates enforce simplicity and anti-abstraction principles, preventing over-engineering at design time.

**Feedback loop:** Constitution gates (simplicity checks, YAGNI enforcement) reject designs that violate principles. Consistency validation ensures the plan does not contradict the spec. Research agents validate technology choices against stated constraints and known limitations.

**What flows back to the specification:** Discovered constraints that were implicit in the requirements, interface definitions that refine vague requirements into concrete contracts, and technology-imposed limitations that narrow the solution space.

---

### Phase 3: Task Breakdown --> DECOMPOSE

**Traditional:** A lead developer or project manager breaks the design into sprint tasks. Task granularity varies by individual judgment. Dependencies between tasks are tracked informally or discovered during implementation. Parallelization opportunities are missed because dependency analysis is manual.

**SDD:** The TASKS command decomposes the plan into atomic units with explicit dependency graphs and parallelization markers. Each task traces to a specific section of the plan and, transitively, to the originating requirement. Tasks include their own acceptance criteria derived from the specification.

**Feedback loop:** Dependency validation identifies circular or missing dependencies before work begins. Parallel execution analysis determines which tasks can proceed simultaneously. Task-to-spec traceability ensures no requirement is orphaned (lacking a corresponding task).

**What flows back to the specification:** Decomposition may reveal that a single requirement is too large or too vague to produce a clean task breakdown. These findings trigger spec refinement -- splitting requirements, adding detail, or clarifying boundaries.

---

### Phase 4: Implementation --> GENERATE

**Traditional:** Developers write code from design documents and requirements, interpreting them through personal understanding. Different developers implement the same requirement differently. Tests are written after or alongside code, and their relationship to requirements is implicit.

**SDD:** AI generates code from the specification with a test-first approach -- acceptance tests are generated before implementation code. The specification provides the full context window: requirements, constraints, interfaces, dependencies, and examples. Drift detection at build time compares generated output against the spec.

**Feedback loop:** Drift detection at build time flags divergence between generated code and specification intent. Test failures trigger investigation -- either the generation was wrong (regenerate with refined constraints) or the spec was incomplete (update the spec). Coverage gaps in generated tests indicate areas where the specification lacks detail.

**What flows back to the specification:** Implementation discoveries -- edge cases not anticipated in the spec, performance characteristics that require new non-functional requirements, interface mismatches that reveal ambiguity in contract definitions.

---

### Phase 5: Testing --> VERIFY

**Traditional:** QA writes test plans and test cases after implementation is complete. Test cases may or may not trace to requirements. Gaps between requirements and test coverage are discovered late, if at all. Regression test suites grow organically without structured connection to the features they validate.

**SDD:** Acceptance scenarios from the specification ARE the tests -- defined before code generation, not after. Verification compares generated code against every requirement in the spec. Validation confirms the spec itself matches user intent. The distinction between verification (code matches spec) and validation (spec matches need) is explicit and tracked separately.

**Feedback loop:** Failed tests trigger a decision: is the code wrong, or is the spec wrong? Coverage analysis identifies requirements without corresponding tests, indicating spec areas that need executable acceptance scenarios. Test results from production-like environments may reveal non-functional gaps in the specification.

**What flows back to the specification:** Failed-test analysis that refines constraints or requirements, coverage gaps that expand the spec's acceptance scenarios, and edge cases discovered during testing that become new explicit requirements.

---

### Phase 6: Deployment --> RELEASE

**Traditional:** CI/CD pipelines are configured separately from the development process. Change logs are manually curated or auto-generated from commit messages, disconnected from the requirements that motivated the changes. Feature flags are managed independently of the specifications they gate. Rollback decisions rely on operational metrics with no structured connection to feature intent.

**SDD:** Deployment is traceable to a specific specification version. Change logs are generated from spec diffs, not commit messages. Feature flags are tied to specification completion status -- a feature is not flagged "on" until its spec's acceptance criteria are verified in the target environment. Operational metrics from deployment become inputs to non-functional requirements in the spec.

**Feedback loop:** Operational metrics (latency, error rates, resource consumption) validate or invalidate the non-functional requirements stated in the spec. SRE monitoring confirms conformance between deployed behavior and specified behavior. Deployment failures trace back to specific spec sections for root cause analysis.

**What flows back to the specification:** Operational baselines that become explicit non-functional requirements, deployment constraints discovered in production (infrastructure limitations, scaling characteristics), and monitoring thresholds derived from observed behavior.

---

### Phase 7: Maintenance --> EVOLVE

**Traditional:** Bug fixes are applied directly to code without updating requirements or design documents. Technical debt accumulates as the gap between documentation and reality widens. Knowledge lives in developers' heads -- when they leave, it leaves with them. Hotfixes bypass the development process entirely.

**SDD:** Maintenance IS evolving specifications. A bug report triggers a spec update (the spec was incomplete or incorrect), which triggers regeneration. Technical debt is visible as specification drift -- places where the code no longer matches the spec. Knowledge is preserved in the specification, not in individuals. Every production incident produces a spec update that strengthens the system for all future work.

**Feedback loop:** Production incidents trigger spec updates that close the gap between specified and actual behavior. Harness strengthening turns every failure into a permanent improvement -- new constraints, new acceptance scenarios, new verification checks. Specification drift analysis identifies areas where code has evolved without corresponding spec updates.

**What flows back to the specification:** Bug-driven requirement refinements, incident-driven constraint additions, and drift corrections that realign spec and implementation.

---

## The Harness Feedback Loop

The most significant structural difference between traditional SDLC and SDD is the harness feedback loop [3]. In traditional development, failures improve the developer's personal knowledge. In SDD, failures improve the specification -- which improves ALL future generations, for all developers.

Two gap types drive harness strengthening:

| Gap Type | Description | Harness Response |
|----------|-------------|-----------------|
| **Spec-to-implementation gap** | Generated code does not match specification intent | Strengthen validation mechanisms: add constraints, tighten acceptance scenarios, improve context provision |
| **Intent-to-specification gap** | Specification does not capture actual user need | Improve elicitation: add structured questions, require domain-expert review, introduce feasibility validation |

Each gap discovered and closed strengthens the harness for ALL future work -- not just the feature that triggered the discovery. This is the compounding return of SDD: the system gets better with every failure.

---

## Summary Matrix

| SDLC Phase | SDD Activity | Feedback Mechanism | What Flows Back to Spec |
|-----------|-------------|-------------------|------------------------|
| **Requirements** | SPECIFY | AI clarification questions, research feasibility checks, spec review gates | Clarification answers, feasibility findings, reviewer comments |
| **Design** | PLAN | Constitution gates, consistency validation, research-validated tech choices | Discovered constraints, interface definitions, technology limitations |
| **Task Breakdown** | DECOMPOSE | Dependency validation, parallel execution analysis, task-to-spec traceability | Requirement splits, added detail, boundary clarifications |
| **Implementation** | GENERATE | Drift detection, test failure analysis, coverage gap identification | Edge cases, performance requirements, interface corrections |
| **Testing** | VERIFY | Failed-test triage (code vs spec), coverage analysis, non-functional gap detection | Refined constraints, expanded acceptance scenarios, new edge-case requirements |
| **Deployment** | RELEASE | Operational metric validation, SRE conformance monitoring, failure traceability | Non-functional requirements, deployment constraints, monitoring thresholds |
| **Maintenance** | EVOLVE | Incident-driven spec updates, harness strengthening, drift analysis | Bug-driven refinements, constraint additions, drift corrections |

---

## Visual

See: [SDD-SDLC Feedback Loop Diagram](../diagrams/sdd-sdlc-feedback.md)

---

## References

1. GitHub spec-kit. "spec-driven.md." -- Bidirectional feedback loops between specification and implementation.
2. Microsoft TechCommunity. "An AI-Led SDLC." -- End-to-end agentic lifecycle with specifications as connective tissue.
3. InfoQ. "Spec-Driven Development at Enterprise Scale." -- Harness feedback loop and gap-driven strengthening.
4. InfoQ. "Spec-Driven Development: When Architecture Becomes Executable." -- Phase transformation from traditional SDLC to spec-driven workflow.
5. CGI. "Spec-Driven Development." -- Continuous conformance monitoring across the deployment lifecycle.
6. Thoughtworks. "Spec-Driven Development." -- SDLC phase transformation and specification as living artifact.

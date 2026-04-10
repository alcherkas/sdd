# Enterprise SDD Adoption

> Last verified: 2026-04-10

---

## Principle

SDD at enterprise scale cannot be treated as a technical rollout. Sustainable adoption requires integration with existing workflows, support for brownfield codebases, and evolution of organizational practices — not just tooling changes.

---

## The Enterprise Challenge

Hari Krishnan's analysis of enterprise SDD adoption [1] identifies a core tension: SDD shifts AI-augmented software delivery from tactical prompting to collaborative intent articulation, but enterprises have existing workflows, tools, and team structures that cannot be discarded overnight.

### Gaps at Enterprise Scale

| Gap | Description |
|-----|-------------|
| **Tooling** | Current SDD tools have gaps at scale — most are optimized for single-developer or small-team workflows |
| **Workflow integration** | Enterprises use Jira, Azure DevOps, Linear, or GitHub Issues — SDD must integrate with these, not replace them |
| **Multi-repo coordination** | Context lives across repos and projects; SDD tools that assume a single codebase miss cross-project dependencies |
| **Cross-functional collaboration** | Product managers, designers, QA, and security teams all need access to specifications — not just developers |

---

## Short-Term Adoption Requirements

Krishnan identifies three requirements for enterprises to experience SDD value in the near term:

### 1. Integration with Existing Workflows

SDD must meet teams where they are. This means:

- **Backlog as communication bridge.** Developers pull stories from Jira, Linear, or Azure DevOps directly into SDD workflows. Progress updates flow back to backlog tools. The backlog serves as the interface between specification work and organizational communication.
- **No forced Git access.** Not every team member needs to interact with specifications through Git. Product teams may need to view and contribute to specs through their existing tools.
- **Incremental adoption.** Teams should be able to start with a single feature or change, not a whole-project migration.

### 2. Support for Brownfield Projects

Most enterprise codebases have no existing specifications. Adoption must account for this:

- **Progressive specification.** Teams write specs for new changes, not for the entire existing system. Over time, the specification base grows organically (the approach taken by OpenSpec's delta specs).
- **No reverse-engineering requirement.** Generating complete specifications from existing code is expensive, error-prone, and immediately stale. Avoid it.
- **Cross-repo context.** At enterprise scale, "brownfield" means context spread across multiple repositories, services, and teams. Tools that assume a single repo miss this.

### 3. Progressive Enablement

Not all teams are ready for the same level of SDD sophistication:

| Maturity Level | Practice | Entry Point |
|----------------|----------|-------------|
| **Level 1** | Requirements before prompting | Add structured requirements to existing tickets |
| **Level 2** | Interface contracts and constraints | Define API specs and coding standards before generation |
| **Level 3** | Full lifecycle with verification | Implement all five SDD phases with automated verification |
| **Level 4** | Cross-team spec governance | Shared specification standards, constitutional constraints |

Teams start at Level 1 and progress as they experience value. Forcing Level 3 practices on a team at Level 1 creates resistance and abandonment.

---

## Long-Term: The Review-Centric Role

As SDD matures in an organization, the developer role shifts from primarily writing code to primarily reviewing AI-generated output against specifications [1].

### What Changes

| Dimension | Before SDD | After SDD |
|-----------|------------|-----------|
| **Primary activity** | Writing code | Reviewing generated code against specs |
| **Spec ownership** | Optional, often neglected | Core responsibility |
| **Quality mechanism** | Code review for correctness | Spec review for completeness + code review for compliance |
| **Context management** | Ad-hoc (what fits in the prompt) | Systematic (structured spec packages) |
| **Collaboration surface** | Pull requests (code) | Specifications (intent) + pull requests (implementation) |

### New Skills Required

Review-centric roles require new skills that are not automatically developed:

1. **Spec evaluation.** The ability to read a specification and identify gaps, ambiguities, and untestable requirements before generation.
2. **Context engineering.** Managing what context reaches the AI agent — too little causes hallucination, too much causes attention loss.
3. **Verification design.** Writing acceptance criteria that can be automatically checked against generated output.
4. **AI output assessment.** Recognizing when AI-generated code is subtly wrong — plausible but incorrect, secure-looking but vulnerable.

---

## Organizational Considerations

### Artifact Location

A key adoption question: where do specification artifacts live?

| Option | Pros | Cons |
|--------|------|------|
| **Monorepo** (specs alongside code) | Version control, atomic commits, developer familiarity | Overcrowding with business use cases, technical designs, tasks, and code |
| **Separate spec repo** | Clean separation, non-developer access easier | Specs drift from code, cross-repo syncing required |
| **Backlog-integrated** (specs in Jira/Linear) | Non-developers can contribute, familiar workflow | Lose version control, harder to feed to AI agents |
| **Hybrid** (specs in repo, linked from backlog) | Best of both — version control + discoverability | Requires tooling to maintain links |

Krishnan's recommendation: use the backlog as a communication bridge while keeping the source-of-truth specifications in the repository. This allows human-AI collaboration without forcing every role into the codebase [1].

### Sustainable Adoption

Sustainable SDD adoption requires treating specifications as **living, shared interfaces** — not static documents that rot after initial creation. This means:

1. **Specs update during verification.** When Phase 5 reveals a gap, the spec is updated, not just the code.
2. **Specs are reviewed in PRs.** Specification changes are part of the pull request, reviewed alongside code changes.
3. **Organizational practices evolve.** Sprint planning includes spec writing. Definition of Done includes spec currency. Retrospectives include spec quality.
4. **First adapt, then evolve.** Organizations should first adapt SDD practices to existing workflows, then evolve toward more AI-native patterns once benefits become evident.

---

## References

1. Krishnan, H. "Spec-Driven Development — Adoption at Enterprise Scale." InfoQ, February 2026.
   https://www.infoq.com/articles/enterprise-spec-driven-development/

2. Krishnan, H. "Spec-Driven Development with Brownfield Projects." Intent-Driven Dev, March 2026.
   https://intent-driven.dev/blog/2026/03/10/spec-driven-development-brownfield/

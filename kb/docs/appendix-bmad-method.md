# BMAD-METHOD: Breakthrough Method for Agile AI-Driven Development

> Last verified: 2026-04-04

---

## Principle

A multi-agent, persona-based SDD framework that treats AI development as a systematic agile process with specialized agents — rather than a single generic conversation. Each agent is a self-contained, version-controlled Markdown artifact with embedded configuration, making institutional knowledge portable and persistent.

---

## Overview

| Attribute | Detail |
|-----------|--------|
| **Full name** | Breakthrough Method for Agile AI-Driven Development |
| **Creator** | BMad Code (Vishal Mysore) |
| **GitHub** | bmad-code-org/BMAD-METHOD (37,000+ stars) |
| **Documentation** | docs.bmad-method.org |
| **License** | MIT (100% free and open source) |
| **Compatible tools** | Claude Code, Cursor, Copilot, OpenAI Codex CLI, and others |
| **Install** | `npx bmad-method@next install` |

---

## Core Innovation: Agent-as-Code

BMAD's defining contribution to SDD is the agent-as-code pattern. Rather than relying on transient prompt engineering or manually configuring each AI session, agents are defined as self-contained Markdown files with embedded YAML configuration.

Each agent file contains:

- **Persona definition.** The role, expertise boundaries, and communication style the agent assumes.
- **Specialized commands.** Operations the agent can perform within its domain.
- **Dependencies.** References to other agents, documents, or artifacts the agent requires.
- **Machine-readable instructions.** Structured directives that constrain agent behavior deterministically.

This approach produces four structural advantages:

1. **Version control.** Agents are code artifacts — diffable, reviewable, and branchable like any source file.
2. **Portability.** An agent defined in one project can be shared across projects and teams without re-training or re-configuration.
3. **Institutional knowledge preservation.** Domain expertise encoded in agent files survives team turnover. Knowledge lives in the repository, not in departed engineers' heads.
4. **Reproducibility.** The same agent file produces consistent behavior across sessions and environments.

---

## Specialized Multi-Agent Personas

| Agent | Role | Phase |
|-------|------|-------|
| **Analyst** | Explores problem space, creates project briefs | Analysis |
| **Product Manager** | Creates PRDs from briefs with comprehensive requirements | Planning |
| **Architect** | Designs system architecture based on PRD constraints | Solutioning |
| **Developer** | Implements based on architecture and story files | Implementation |
| **QA Engineer** | Testing, quality assurance, adversarial review | Implementation |
| **Scrum Master** | Story breakdown, acceptance criteria, prioritization | Planning |
| **Custom agents** | User-defined via BMad Builder (BMB) | Any |

Each agent operates within its designated phase and expertise boundary. The Analyst does not write code; the Developer does not redefine requirements. This separation mirrors established agile role boundaries and prevents the role-confusion problems that occur when a single AI session attempts to handle all aspects of development simultaneously.

---

## Party Mode

Party Mode allows multiple agent personas to interact within a single session simultaneously. Rather than switching between agents sequentially, the framework orchestrates cross-functional discussions where agents respond in character.

**How it works:**

- The BMad Master orchestrator evaluates each user message and selects the 2-3 most relevant agents to respond.
- Selected agents respond in character — agreeing, disagreeing, and building on each other's positions.
- The user observes and directs the multi-agent conversation rather than managing individual agents.

**Use cases where Party Mode adds value:**

- Tradeoff debates (e.g., Architect vs. Product Manager on complexity vs. feature scope).
- Testing strategy alignment (QA Engineer challenging Developer assumptions).
- Architecture reviews with cross-functional input.
- Sprint planning where Scrum Master, Developer, and Architect negotiate story scope.

---

## Four-Phase Workflow

| Phase | Name | Input | Output | Required |
|-------|------|-------|--------|----------|
| 1 | **Analysis** | Problem statement, domain context | Project brief | Optional |
| 2 | **Planning** | Project brief or direct requirements | PRD, user stories, acceptance criteria | Yes |
| 3 | **Solutioning** | PRD and constraints | Architecture doc, implementation guidelines | Yes |
| 4 | **Implementation** | Architecture + story files | Code, tests | Yes |

**Phase 1 — Analysis.** The Analyst agent explores the problem space through structured brainstorming. Output: a project brief that frames the problem, identifies stakeholders, and establishes success criteria. This phase is optional — skip it when the problem is already well-understood.

**Phase 2 — Planning.** The Product Manager agent transforms the brief into a PRD with comprehensive requirements. The Scrum Master agent breaks the PRD into user stories with acceptance criteria and prioritization. This phase is required — it produces the specification artifacts that constrain all downstream work.

**Phase 3 — Solutioning.** The Architect agent designs system architecture based on PRD constraints. Output: architecture document and implementation guidelines that the Developer agent will follow. Decisions about technology choices, component boundaries, and data flow are made here — not during implementation.

**Phase 4 — Implementation.** The Developer agent builds incrementally, one story at a time. The QA Engineer agent reviews output against acceptance criteria. Each story is implemented and verified before the next begins.

**Quick Flow alternative.** For straightforward work that does not require full lifecycle coverage, Quick Flow skips phases 1-3 and proceeds directly to implementation. Appropriate for small changes, bug fixes, or well-understood tasks where a full PRD would be overhead.

---

## Context Preservation and Token Efficiency

BMAD addresses the context window problem through a tiered content architecture and document sharding strategy.

**Three-tier content model:**

| Tier | Lifecycle | Example | Persistence |
|------|-----------|---------|-------------|
| **Ephemeral** | Temporary, single-session | Brainstorming output, scratch analysis | Discarded after use |
| **Transient** | Implementation-specific | Sprint status, in-progress story context | Retained during active development |
| **Permanent** | Core project documentation | PRD, architecture doc, completed stories | Retained indefinitely |

**Token efficiency mechanisms:**

- **Document sharding.** Large files are split into LLM-optimized pieces that fit within effective attention windows.
- **Story files as knowledge packages.** Each story file contains the full context needed for its scope — the Developer agent loads one story at a time rather than the entire PRD.
- **Selective loading.** Phase 4 agents load only the relevant story file and sprint status, not the complete architecture or PRD.
- **Measured improvement.** BMAD v6 reports a 90% token reduction compared to earlier versions through these sharding and selective-loading techniques.

---

## Expansion Packs

BMAD uses a modular expansion pack system for domain-specific capabilities.

| Pack | Focus | Example Capabilities |
|------|-------|---------------------|
| **BMM** | Core agile development | Full software development lifecycle |
| **TEA** | Testing | Risk-based prioritization, 9+ testing workflows |
| **CIS** | Creative intelligence | Innovation exercises, creative thinking frameworks |
| **BMGD** | Game development | Unity, Unreal, Godot support |
| **BMB** | Agent builder | Framework for creating custom agents and modules |

Expansion packs are installed alongside the core method and activated per-project based on domain requirements. They extend the agent roster and command set without modifying the core workflow.

---

## When to Use BMAD

| Scenario | Fit | Rationale |
|----------|-----|-----------|
| Complex greenfield projects | Excellent | Full lifecycle coverage across all four phases; multi-agent depth prevents blind spots |
| Cross-domain (games, creative, DevOps) | Excellent | Expansion packs provide specialized agents for non-standard domains |
| Enterprise with large teams | Excellent | Agent-as-code preserves institutional knowledge; agents are shared across teams |
| Quick brownfield changes | Overkill | Lightweight frameworks (e.g., OpenSpec) handle targeted modifications more efficiently |
| Small focused features | Overkill | Direct prompting or a light spec is sufficient; BMAD's multi-phase workflow adds unnecessary overhead |
| Single-developer side projects | Mixed | Powerful capabilities, but the setup cost and multi-agent workflow may exceed the complexity of the work |

---

## References

1. BMad Code. BMAD-METHOD. GitHub.
   https://github.com/bmad-code-org/BMAD-METHOD
2. BMad Code. BMAD-METHOD Documentation.
   https://docs.bmad-method.org
3. Mysore, V. "What is BMAD-METHOD." Medium.
4. Mysore, V. "BMAD-Method: From Zero To Hero." Medium.
5. "A Tale of Two Frameworks: BMAD vs GitHub Spec Kit." Medium.
6. "Comprehensive Guide to SDD: Kiro, Spec Kit, BMAD." Medium.
7. "BMAD: The Agile Framework That Makes AI Actually Predictable." DEV Community.

# OpenSpec: Lightweight Spec-Driven Development

> Last verified: 2026-04-04

---

## Overview

OpenSpec is an open-source, lightweight SDD framework created by **Fission-AI** and designed for brownfield-first specification development. Its core philosophy: "Add a lightweight spec layer so you agree on what to build before any code is written."

| Attribute | Detail |
|-----------|--------|
| Creator | Fission-AI |
| Repository | [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| Website | [openspec.dev](https://openspec.dev) |
| License | Open source |
| Requirements | No API keys, no MCP server |
| Agent support | 24+ assistants (Claude Code, Cursor, Copilot, Windsurf, Cline, Roo Code, JetBrains, others) |

OpenSpec occupies a specific niche in the SDD landscape: minimal-overhead specification for teams working on existing codebases. It does not attempt to be a full lifecycle orchestrator or a multi-agent framework.

---

## Core Innovation: Delta Specs

The defining technical contribution of OpenSpec is the **delta spec** -- a specification format that captures only what is changing, not the entire system state.

### Structure

Every delta spec uses a three-part format:

| Section | Purpose |
|---------|---------|
| **ADDED Requirements** | New behavior, endpoints, components, or constraints introduced by the change |
| **MODIFIED Requirements** | Existing behavior that changes in scope, interface, or implementation |
| **REMOVED Requirements** | Behavior, endpoints, or components being deprecated or deleted |

### Why Delta Specs Matter

Traditional specification approaches restate the entire system specification for every change. This creates three problems at scale:

1. **Token cost.** Full-spec restating burns context window capacity on unchanged requirements. Delta specs transmit only the diff.
2. **Context bloat.** Large specifications push working context out of the window. Delta specs keep the active context small and focused.
3. **Audit opacity.** When the full spec changes, it is difficult to identify what actually changed. Delta specs make every modification atomic and reviewable.

Delta specs are the key differentiator between OpenSpec and other SDD frameworks such as spec-kit (which uses full specification files) or BMAD-METHOD (which uses persona-scoped documents).

---

## Brownfield-First Approach

Most SDD frameworks optimize for greenfield projects -- building from scratch with a clean specification. OpenSpec inverts this assumption. It targets the **1-to-n problem**: maintaining and evolving existing systems where a complete upfront specification does not exist and reverse-engineering one is impractical.

### Design Decisions

| Decision | Rationale |
|----------|-----------|
| No full-codebase reverse-engineering | Generating a complete spec from an existing codebase is expensive, error-prone, and immediately stale |
| Source of Truth in `specs/` | Main specifications accumulate incrementally as changes are merged |
| Change Proposals in `changes/` | Each feature lives in filesystem isolation until completion |
| Incremental specification growth | The spec base grows only through merged delta specs, never through bulk generation |

This approach means teams can adopt OpenSpec on an existing codebase without a migration phase. The first change proposal creates the first spec. Over time, the `specs/` directory accumulates a progressively complete specification of the system.

---

## Architecture

OpenSpec uses a flat directory structure with clear separation between stable specifications and in-progress work:

```
openspec/
├── specs/                # Source of truth (main specifications)
├── changes/              # Active feature proposals (isolated per change)
│   └── archive/          # Completed and merged changes
├── project.md            # Global technical context
└── AGENTS.md             # AI behavioral instructions
```

### Change Folder Contents

Each change folder under `changes/` contains four artifacts:

| File | Role |
|------|------|
| `proposal.md` | Problem statement, scope, acceptance criteria |
| `specs/` | Delta specs (ADDED / MODIFIED / REMOVED) |
| `design.md` | Technical approach, architecture decisions |
| `tasks.md` | Implementation tasks, ordered and checkable |

This per-change isolation prevents work-in-progress from contaminating the source of truth. Multiple changes can proceed in parallel without merge conflicts in the spec layer.

---

## Workflow: Propose, Apply, Archive

OpenSpec follows a three-phase workflow:

```
Propose  -->  Apply  -->  Archive
(define)     (build)     (merge)
```

1. **Propose.** Create a new change with all artifacts -- proposal, delta specs, design, and tasks.
2. **Apply.** Execute the implementation tasks defined in the change.
3. **Archive.** Finalize the change, merge delta specs into the main `specs/` directory, and move the change folder to `changes/archive/`.

### Command Syntax

Command syntax varies by assistant:

| Assistant | Syntax | Example |
|-----------|--------|---------|
| Claude Code | `/opsx:command` | `/opsx:propose my-feature` |
| Cursor, Windsurf | `/opsx-command` | `/opsx-propose my-feature` |

---

## Expanded Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/opsx:explore` | Investigate unclear requirements | Before proposing changes |
| `/opsx:propose` | Create change with all artifacts | Starting new work |
| `/opsx:apply` | Execute implementation tasks | Ready to implement |
| `/opsx:verify` | Validate implementation matches artifacts | Post-implementation |
| `/opsx:archive` | Finalize and merge deltas | Work complete |
| `/opsx:sync` | Manually merge delta specs into main specs | Spec maintenance |
| `/opsx:bulk-archive` | Archive multiple changes at once | Cleanup |
| `/opsx:onboard` | Interactive tutorial for new users | First-time setup |

The command set is deliberately small. Each command maps to a single phase transition in the workflow, reducing cognitive overhead and making automation straightforward.

---

## Design Principles

OpenSpec is built on five explicit design principles:

| Principle | Meaning |
|-----------|---------|
| **Fluid not rigid** | No rigid phase gates; developers move between propose, apply, and archive as needed |
| **Iterative not waterfall** | Continuous refinement of specs and implementation; no big-bang specification phase |
| **Easy not complex** | Minimal process overhead; a developer should be productive within minutes |
| **Brownfield not just greenfield** | Designed for existing codebases first; greenfield is a supported secondary case |
| **Scalable** | Works for personal projects and scales to enterprise teams |

These principles distinguish OpenSpec from heavier SDD frameworks that enforce strict phase ordering or require extensive initial configuration.

---

## When to Use OpenSpec

| Scenario | Fit | Why |
|----------|-----|-----|
| Brownfield iteration | Excellent | Delta specs are purpose-built for capturing changes to existing systems |
| Day-to-day feature work | Excellent | Lightweight process; minimal overhead per change |
| Greenfield from scratch | Adequate | Works, but spec-kit or BMAD-METHOD may provide more structure for initial architecture |
| Enterprise governance | Limited | No constitutional enforcement, audit trails, or compliance hooks |
| Complex multi-agent projects | Limited | Single-agent focused; no built-in agent orchestration or persona management |

OpenSpec is strongest when the primary concern is keeping AI-assisted changes bounded, auditable, and low-overhead. It is weakest when the project requires formal governance or multi-agent coordination.

---

## References

1. Fission-AI. "OpenSpec." GitHub.
   https://github.com/Fission-AI/OpenSpec
2. OpenSpec. Project website.
   https://openspec.dev
3. Fowler, M. "Understanding SDD -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
4. Intent-Driven Development. Guide.
   https://intent-driven.dev

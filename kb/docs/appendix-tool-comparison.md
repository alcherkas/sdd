# SDD Tool Ecosystem Comparison

> Last verified: 2026-04-04

---

The SDD landscape includes **frameworks** (how to organize specs), **IDEs** (where to write them), and **toolkits** (what automates the workflow). Choosing the right combination depends on project type, team size, and existing codebase. No single tool dominates every scenario; understanding the trade-offs is essential.

---

## 1. Framework Comparison

| Aspect | Spec Kit (GitHub) | OpenSpec (Fission-AI) | BMAD-METHOD | Kiro (AWS) | Tessl |
|--------|-------------------|----------------------|-------------|------------|-------|
| **Philosophy** | Constitutional governance | Lightweight transactional | Multi-agent personas | Built-in IDE phases | Spec-as-source registry |
| **Best for** | Enterprise greenfield (0-to-1) | Brownfield iteration (1-to-n) | Complex multi-domain | Teams wanting integrated IDE | Managed spec versioning |
| **Setup complexity** | Moderate | Low | Higher | Low (IDE) | Moderate |
| **Workflow** | Specify - Plan - Tasks - Implement | Propose - Apply - Archive | Analysis - Planning - Solutioning - Implementation | Requirements - Design - Implement | Spec - Generate - Deploy |
| **Open source** | Yes | Yes | Yes (MIT) | No | Partial |
| **GitHub stars** | ~39K | ~4K | ~37K | N/A | N/A |
| **AI tool support** | Claude, Copilot, Cursor, Gemini, others | 24+ assistants | Claude Code, Cursor, Copilot, Codex | Claude (built-in) | Multiple |
| **Spec format** | Markdown templates with constitution | Delta specs (ADDED / MODIFIED / REMOVED) | Persona-driven markdown + YAML | Structured IDE panels | Registry-managed |
| **Context efficiency** | Verbose (larger token use) | Minimal (delta-based) | Optimized (90% reduction in v6) | Auto-managed | Managed |
| **Governance** | Constitution with 9 articles | Per-change proposal review | Agent-level role enforcement | Phase gates | Registry governance |
| **Brownfield support** | Adequate | Excellent | Good | Adequate | Good |

---

## 2. Decision Guide: When to Use What

| Your Situation | Recommended Tool | Why |
|----------------|------------------|-----|
| Starting new project, need comprehensive planning | Spec Kit or BMAD | Full lifecycle coverage from requirements through implementation |
| Iterating on existing codebase | OpenSpec | Delta specs minimize overhead; brownfield-first design |
| Want SDD built into the IDE | Kiro | No separate tooling needed; phases managed within the editor |
| Complex project, multiple domains | BMAD | Expansion packs and multi-agent persona system handle cross-domain coordination |
| Enterprise with governance needs | Spec Kit | Constitutional enforcement with 9 articles; audit-oriented |
| Minimal overhead, just want specs | OpenSpec | Lightest weight; productive within minutes |
| Need managed spec versioning | Tessl | Registry-based approach with version control built in |
| Quick prototype, single feature | Direct prompting | No framework needed; add structure later if the project grows |

---

## 3. Combining Frameworks

You do not have to pick just one. The frameworks occupy different layers of the workflow and can be composed.

**Common combinations:**

- **OpenSpec for daily feature work + Spec Kit constitution for project-wide standards.** Use OpenSpec's lightweight delta specs for individual changes while Spec Kit's constitution enforces architectural invariants and governance rules across the project.

- **BMAD for planning phases + OpenSpec for implementation tracking.** Use BMAD's multi-agent personas (Analyst, Architect, Product Manager) during discovery and design, then switch to OpenSpec's propose-apply-archive cycle for implementation-level specification.

- **Kiro IDE for individual work + Spec Kit for team governance.** Developers use Kiro's built-in phase management for local productivity, while the team maintains a Spec Kit constitution in the shared repository for cross-cutting standards.

The key constraint when combining frameworks is to avoid duplicating the source of truth. Establish which tool owns each artifact type and enforce that boundary.

---

## 4. IDE and AI Tool Support Matrix

| AI Tool | Spec Kit | OpenSpec | BMAD | Kiro |
|---------|----------|----------|------|------|
| Claude Code | Yes | Yes | Yes | N/A |
| Cursor | Yes | Yes | Yes | N/A |
| GitHub Copilot | Yes | Yes | Yes | N/A |
| Windsurf | Yes | Yes | Yes | N/A |
| JetBrains | -- | Yes | -- | N/A |
| Kiro IDE | -- | -- | -- | Built-in |

**Notes:**

- "N/A" for Kiro indicates that Kiro is itself an IDE and does not integrate as a plugin into other tools.
- JetBrains support for OpenSpec comes through its broad assistant compatibility (24+ assistants). Spec Kit and BMAD primarily target VS Code-based editors and CLI agents.
- All markdown-based frameworks (Spec Kit, OpenSpec, BMAD) are technically usable in any editor -- the table reflects officially documented and tested integrations.

---

## 5. Maturity and Adoption

| Framework | Maturity | Community Size | Documentation Quality |
|-----------|----------|----------------|----------------------|
| Spec Kit | Stable | Large (~39K stars); backed by GitHub | High; comprehensive templates and examples |
| OpenSpec | Early-to-stable | Growing (~4K stars) | Good; clear guides, active iteration |
| BMAD-METHOD | Stable | Large (~37K stars) | High; detailed persona docs and expansion packs |
| Kiro | Early | AWS-backed; limited public community metrics | Moderate; IDE-embedded docs, less community content |
| Tessl | Early | Limited public data | Moderate; registry-focused documentation |

The SDD tooling space is evolving rapidly. Star counts and maturity assessments reflect a snapshot in time. Fowler's analysis provides a useful independent evaluation of Kiro, Spec Kit, and Tessl as representative approaches, noting that the field has not yet converged on a single dominant pattern.

---

## 6. References

1. Fowler, M. "Understanding SDD Tools -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
2. GitHub. "spec-kit." GitHub.
   https://github.com/github/spec-kit
3. Fission-AI. "OpenSpec." GitHub.
   https://github.com/Fission-AI/OpenSpec
4. bmad-code-org. "BMAD-METHOD." GitHub.
   https://github.com/bmad-code-org/BMAD-METHOD
5. Mysore, V. "Comprehensive Guide to SDD: Kiro, Spec Kit, BMAD." Medium.
6. Augment Code. "Best Spec-Driven Development Tools for AI Coding in 2026."
7. avasdream.com. "OpenSpec vs Spec Kit."

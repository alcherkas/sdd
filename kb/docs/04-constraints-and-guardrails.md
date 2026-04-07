# Phase 3: Constraints and Guardrails

> **Last verified: 2026-04-04**

---

## Principle

Explicitly define what the AI must and must not do. Without constraints, AI agents
optimize for plausible-looking code, not correct code. Constraints are the human's
primary leverage point in the SDD lifecycle -- they bound the solution space so that
generation produces predictable, correct, and secure output [1][2].

---

## Types of Constraints

| Type | Purpose | Example |
|------|---------|---------|
| **Coding Standards** | Enforce language conventions, naming patterns, and file structure | "Use camelCase for variables, PascalCase for components. One component per file." |
| **Framework Constraints** | Lock generation to specific libraries, patterns, and versions | "Use React 19 Server Components. No client-side state management libraries." |
| **Security Requirements** | Mandate input validation, auth patterns, and prohibit dangerous APIs | "All user input must be validated with zod schemas. Never use `eval()` or `Function()` constructor." |
| **Performance Budgets** | Set measurable limits on response times, bundle sizes, and memory | "API responses under 200ms at p95. JavaScript bundle under 150KB gzipped." |
| **The "Never Do" List** | Explicit prohibitions that prevent common AI drift patterns | "Never use lodash. Never add dependencies without approval. Never use `any` type." |

The "Never Do" list deserves special attention. AI agents are trained to be helpful,
which means they default to adding capabilities rather than refusing them. Explicit
prohibitions counteract this bias. Osmani identifies the "Never Do" list as one of the
highest-leverage sections of any spec, because it prevents the most common classes of
AI-generated defects before they occur [1].

---

## The Three-Tier Permission Model

Addy Osmani defines a three-tier model for AI agent permissions that maps directly
to the Constrain phase of SDD [1]. The tiers partition all possible agent actions into
three categories:

| Tier | Definition | Examples |
|------|-----------|----------|
| **Always** | Actions the AI can take without asking. These are safe, reversible, and expected. | Run tests, format code, lint, read project files, generate code in designated directories |
| **Ask First** | Actions that require explicit human approval before execution. These are consequential but sometimes necessary. | Install new packages, modify configuration files, change database schemas, add new API endpoints |
| **Never** | Hard stops that the AI must not take under any circumstances. These are irreversible or high-risk. | Delete files outside working directory, push to main/production, modify CI/CD secrets, disable security controls, run `rm -rf` |

The three-tier model is not just a safety mechanism. It is a productivity tool. When
the AI knows which actions are pre-approved, it can execute them without pausing for
confirmation, which reduces friction in the generation phase. The boundaries must be
set during the Constrain phase, not negotiated during generation [1].

### Setting the Tiers

The tier assignments depend on the risk profile of the project:

| Project Type | Always (broader) | Ask First | Never (stricter) |
|---|---|---|---|
| Personal prototype | Run any command, install packages | Delete files, modify system configs | Push to production, access credentials |
| Team project | Run tests, format, lint | Install packages, modify shared configs | Push to main, modify CI, change auth |
| Enterprise / regulated | Run tests (read-only) | Any file modification, any command | Everything not explicitly allowed |

The general principle: **default to restrictive, then expand the "Always" tier as trust
is established** through successful verification cycles [1][2].

---

## Encoding Constraints for AI Tools

Constraints are only effective if the AI agent actually reads them. Each major tool
has a specific mechanism for loading project-level instructions:

| Mechanism | Tool | Scope | Persistence |
|-----------|------|-------|-------------|
| `CLAUDE.md` | Claude Code | Project root and subdirectories | Loaded automatically on every session. Supports hierarchical files (root + subdirectory overrides). Deny rules via `#deny` directives. |
| `.cursorrules` | Cursor | Project root | Loaded automatically when project is opened. Plain text rules and conventions. |
| `copilot-instructions.md` | GitHub Copilot | Repository (`.github/` directory) | Loaded as system context for Copilot Chat. Supports workspace and per-language instructions. |
| `specs/*.md` | GitHub spec-kit | Per-feature or per-task | Loaded on demand. Constraints embedded directly in spec documents alongside requirements. |
| Kiro built-in | AWS Kiro | IDE-managed | Constraints defined in the requirements and design phases of Kiro's built-in workflow. |
| In-spec constraints | Any tool | Per-spec document | Embedded directly in the specification file. Portable across tools. |

### CLAUDE.md: Deny Rules and Allowed Commands

Claude Code's `CLAUDE.md` supports explicit deny rules that act as hard constraints [3]:

```markdown
# Project Instructions

## Allowed commands
- npm test
- npm run lint
- npm run build

## Deny rules
Never modify files in /infrastructure/
Never run commands that access production databases
Never install packages without listing them in the spec first
```

These rules are loaded into the system prompt on every session and persist across
conversations. They function as the "Never" tier of the permission model.

### .cursorrules: Project Conventions

Cursor loads `.cursorrules` from the project root as contextual instructions [4]:

```markdown
# Project Rules

- Use TypeScript strict mode
- All API handlers must validate input with zod
- Use server components by default; client components only when interactivity is required
- Never use default exports
- Test files must be co-located with source files
```

### In-Spec Constraints

The most portable approach is embedding constraints directly in the specification
document. This works with any tool because the constraints travel with the spec:

```markdown
## Constraints

- Language: TypeScript 5.x, strict mode
- Framework: Next.js 15 with App Router
- State: Server components only; no client state libraries
- Auth: NextAuth.js v5; all routes authenticated by default
- Testing: Vitest + Testing Library; minimum 80% branch coverage
- NEVER: No lodash, no moment.js, no class components, no `any` type
```

In-spec constraints have the advantage of being reviewable in pull requests and
versioned alongside the code they govern [2][6].

---

## Linter and CI as Automated Constraint Enforcement

Constraints written in natural language are enforced by the AI's compliance. Constraints
encoded in linters and CI pipelines are enforced by tooling that cannot be talked out
of its rules.

The principle: **every constraint that can be automated, should be automated**.
AI-generated code must pass the same CI pipeline as human-written code [2][5].

| Enforcement Layer | Tool Examples | What It Catches |
|---|---|---|
| **Type checking** | TypeScript `tsc --strict`, mypy, Ruff | Type errors, missing null checks, incorrect interfaces |
| **Linting** | ESLint, Biome, Ruff, clippy | Style violations, banned APIs, import restrictions, complexity limits |
| **Formatting** | Prettier, Black, rustfmt | Consistent formatting (eliminates noise in diffs) |
| **Security scanning** | Semgrep, Bandit, CodeQL | Known vulnerability patterns, unsafe API usage |
| **Dependency auditing** | `npm audit`, `pip-audit`, Snyk | Vulnerable or prohibited dependencies |
| **Test suite** | Jest, Vitest, pytest | Functional regressions, spec-compliance failures |
| **Bundle/performance** | Lighthouse CI, bundlesize | Performance budget violations |

### Why Automated Enforcement Matters for AI Code

AI-generated code has a **1.7x higher issue rate** than human-written code [7]. This
makes automated enforcement more important, not less. The CI pipeline is the final
constraint enforcement layer -- it catches what natural-language constraints and human
review miss.

A practical configuration:

1. **Pre-commit hooks** run linting and formatting on every commit (fast feedback).
2. **CI pipeline** runs the full suite: type checking, linting, security scanning,
   tests, and performance checks (comprehensive verification).
3. **Required status checks** on the main branch prevent merging code that fails any
   automated constraint.

The AI agent should be configured to run pre-commit checks locally before presenting
code as complete. In Claude Code, this is done via `CLAUDE.md` allowed commands; in
Cursor, via task configurations [3][4].

---

## References

1. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. Anthropic. "Claude Code Documentation: CLAUDE.md." 2025-2026.
   https://docs.anthropic.com/en/docs/claude-code/memory#claudemd

4. Cursor. "Rules for AI." 2025-2026.
   https://docs.cursor.com/context/rules-for-ai

5. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

6. Fowler, M. "Understanding SDD -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

7. CodeRabbit. "State of AI vs Human Code Generation Report." December 2025.
   https://www.coderabbit.ai/blog/state-of-ai-vs-human-code-generation-report

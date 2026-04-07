# SDD with GitHub Copilot

> How to practice Spec-Driven Development with GitHub Copilot (including Agent Mode and CLI).

Last verified: 2026-04-04

---

### Repository Instructions

**Why**: Copilot reads `.github/copilot-instructions.md` for persistent context. This is your project-wide constraint layer -- every Copilot session in the repo will receive these instructions.

**How**:
- Create `.github/copilot-instructions.md` at the repo root.
- Include: project description, coding standards, architectural constraints, testing requirements, and "never do" rules.
- Keep instructions specific and actionable. Vague guidance gets ignored.
- Update instructions when your project's spec or architecture evolves.

Source: [GitHub docs -- Copilot Instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot)

---

### spec-kit Integration

**Why**: spec-kit is GitHub's official SDD toolkit, designed specifically for Copilot. It provides a structured workflow from specification through implementation, with built-in consistency checks.

**How**:
- Install spec-kit in your VS Code environment.
- Use `/speckit.specify` to create a feature specification from a plain-language description.
- Use `/speckit.plan` to generate an implementation plan from the spec.
- Use `/speckit.tasks` to break the plan into discrete, executable tasks.
- Specs and plans are stored as artifacts in your repo, creating an audit trail.

Source: [spec-kit repository](https://github.com/github/spec-kit)

---

### Agent Mode for Implementation

**Why**: Copilot Agent Mode can execute multi-step implementations from specs. It reads files, writes code, runs terminal commands, and iterates -- all guided by your spec.

**How**:
- Reference spec files explicitly in agent prompts (e.g., "Implement the feature described in specs/auth-flow.md").
- Use `@workspace` for broad context when the implementation touches many files.
- Let Agent Mode generate tests first, then implementation.
- Review each step -- Agent Mode proposes changes but waits for approval before applying.

Source: [GitHub docs -- Copilot Agent Mode](https://docs.github.com/en/copilot/using-github-copilot/using-copilot-agent-mode)

---

### Constitution for Governance

**Why**: spec-kit's `constitution.md` enforces architectural principles across all specs. It acts as a meta-spec -- the rules that all other specs must follow.

**How**:
- Configure `memory/constitution.md` with your project's immutable principles (via spec-kit).
- Include: architectural boundaries, dependency rules, performance constraints, security requirements.
- The constitution is checked during `/speckit.analyze` -- violations are flagged before implementation begins.
- Update the constitution rarely and deliberately. It represents your project's core invariants.

Source: [spec-kit docs -- Constitution](https://github.com/github/spec-kit)

---

### Copilot CLI for Spec-Driven Scripting

**Why**: Copilot CLI (`gh copilot`) brings spec-driven workflows to the terminal, useful for CI/CD integration and automation.

**How**:
- Use `gh copilot suggest` to generate commands that align with your spec's deployment and testing requirements.
- Use `gh copilot explain` to verify that generated scripts match spec intent.
- Integrate Copilot CLI into CI pipelines for spec-aware automation.

Source: [GitHub docs -- Copilot CLI](https://docs.github.com/en/copilot/using-github-copilot/using-github-copilot-in-the-command-line)

---

### Recommended Workflow

1. `/speckit.specify` -- create a feature specification from a plain-language description.
2. Review and refine the spec. Use `/speckit.clarify` to resolve ambiguities.
3. `/speckit.plan` -- generate an implementation plan from the spec.
4. `/speckit.tasks` -- break the plan into executable tasks with dependency order.
5. Implement tasks in dependency order using Agent Mode, referencing the spec.
6. Run `/speckit.analyze` to check cross-artifact consistency.
7. Verify implementation against the spec. Update the spec if requirements changed.

---

### spec-kit Commands Summary

| Command | Purpose |
|---|---|
| `/speckit.specify` | Create a feature spec from a plain-language description |
| `/speckit.plan` | Generate an implementation plan from a spec |
| `/speckit.tasks` | Break a plan into discrete, executable tasks |
| `/speckit.clarify` | Resolve ambiguities and open questions in a spec |
| `/speckit.analyze` | Cross-artifact consistency check across specs and code |
| `/speckit.constitution` | View or edit the project's immutable architectural principles |

Source: [spec-kit repository](https://github.com/github/spec-kit)

---

### Checklist Summary

- [ ] `.github/copilot-instructions.md` exists with project constraints and SDD rules.
- [ ] spec-kit is installed and the team knows the core commands.
- [ ] `memory/constitution.md` defines immutable architectural principles.
- [ ] Specs are created with `/speckit.specify` before implementation begins.
- [ ] Implementation plans are generated with `/speckit.plan` and reviewed.
- [ ] Agent Mode references spec files explicitly in every implementation prompt.
- [ ] `/speckit.analyze` is run before merging to check cross-artifact consistency.
- [ ] Tests are generated before implementation code.
- [ ] Spec changes are tracked and deliberate, not side effects of code generation.

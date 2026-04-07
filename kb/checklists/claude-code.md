# SDD with Claude Code

> How to practice Spec-Driven Development with Claude Code (CLI and IDE extensions).

Last verified: 2026-04-04

---

### Project Setup

**Why**: Claude Code reads CLAUDE.md on every session start -- this is your persistent constraint layer. Without it, each session starts from zero and the AI has no awareness of your project's rules.

**How**:
- Create `CLAUDE.md` at the repo root.
- Include: project description, coding standards, framework constraints, "never do" rules, testing requirements.
- Reference spec files or spec directories so Claude always knows where specs live.
- Keep CLAUDE.md concise -- it is read on every invocation, so bloat degrades performance.

Source: [Claude Code docs -- CLAUDE.md](https://docs.anthropic.com/en/docs/claude-code/memory)

---

### Plan Mode for Requirements

**Why**: Plan Mode (Shift+Tab) prevents premature implementation -- the AI explores and reasons before writing code. This aligns with SDD's principle of specification before implementation.

**How**:
- Start every feature with Plan Mode.
- Write a short requirements brief (plain language, bullet points are fine).
- Let Claude expand the brief into a full spec: data model, API surface, edge cases, error handling.
- Review the expanded spec. Challenge assumptions, add missing constraints.
- Only switch to implementation mode once the spec is stable.

Source: [Claude Code docs -- Plan Mode](https://docs.anthropic.com/en/docs/claude-code)

---

### Using Memory for Persistent Specs

**Why**: Memory files persist across sessions, keeping spec context available without re-pasting. This prevents spec drift between sessions.

**How**:
- Store active specs in Claude's memory system or as files referenced from CLAUDE.md.
- For long-running features, keep the spec as a standalone markdown file and point to it in CLAUDE.md.
- When a spec changes, update both the spec file and any memory references.

Source: [Claude Code docs -- Memory](https://docs.anthropic.com/en/docs/claude-code/memory)

---

### Permission Controls as Constraints

**Why**: Claude Code's permission system enforces the "Never" tier of the three-tier model. Permissions act as hard guardrails that no prompt can override.

**How**:
- Configure `settings.json` with deny rules for dangerous operations (e.g., force-push, deleting production configs).
- Use allowlists for approved commands (e.g., only `npm test`, `npm run lint`).
- Set file-level permissions to prevent modification of spec files during implementation phases.
- Review permissions periodically -- they should mirror your spec's "Never" constraints.

Source: [Claude Code docs -- Permissions](https://docs.anthropic.com/en/docs/claude-code/settings)

---

### Hooks for Automated Verification

**Why**: Hooks run automatically on tool use -- enabling automated spec compliance checks without manual intervention. This closes the gap between "spec says X" and "code does X."

**How**:
- Configure pre-commit hooks to run linters, type checkers, and test suites automatically.
- Add hooks that validate generated code against spec constraints (e.g., no new dependencies without approval).
- Use `PostToolUse` hooks to verify outputs after file writes or command execution.
- Keep hooks fast -- slow hooks break the development flow.

Source: [Claude Code docs -- Hooks](https://docs.anthropic.com/en/docs/claude-code/hooks)

---

### Slash Commands and Custom Skills

**Why**: Custom slash commands automate repetitive SDD workflow steps, reducing friction and ensuring consistency.

**How**:
- Create custom commands in `.claude/commands/` for repeated SDD patterns (e.g., `/spec-review`, `/gen-tests`).
- Use spec-kit or OpenSpec slash commands if installed.
- Build skills that chain multiple steps: read spec, generate tests, generate implementation, run verification.
- Share commands across the team by committing `.claude/commands/` to the repo.

Source: [Claude Code docs -- Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)

---

### Recommended Workflow

1. Write a requirements brief (plain text, concise).
2. Enter Plan Mode -- expand the brief into a full spec.
3. Review the spec against your project's checklist and constraints.
4. Switch to implementation mode -- generate tests first (spec becomes the test oracle).
5. Generate implementation code.
6. Run hooks and verify output against the spec.
7. If the spec needs updating, update it explicitly and regenerate affected code.

---

### Checklist Summary

- [ ] CLAUDE.md exists at repo root with project constraints.
- [ ] Spec files are stored in a known location and referenced from CLAUDE.md.
- [ ] Plan Mode is used before any implementation begins.
- [ ] Permission controls block operations that violate spec constraints.
- [ ] Hooks run linters, type checks, and tests on every tool use.
- [ ] Custom slash commands exist for repeated SDD steps.
- [ ] Tests are generated before implementation code.
- [ ] Spec is updated explicitly when requirements change.

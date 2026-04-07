# Spec Review Checklist

> Last verified: 2026-04-04

> Run through this checklist before feeding a spec to an AI agent. A spec that fails these checks will produce unreliable output.

## Pre-Generation Review

### Completeness
- [ ] Goal statement present (1-2 sentences, answers "what" and "why")
- [ ] User context defined (who needs this and their situation)
- [ ] All functional requirements listed
- [ ] Non-functional requirements stated (performance, security, accessibility)
- [ ] Success criteria defined and testable
- [ ] Out of scope explicitly stated

**Why**: Incomplete specs force the LLM to guess. Every guess is a potential deviation from your intent.
**How**: Before generation, scan the spec for each item above. If any is missing, add it -- even a one-line statement is better than silence.

### Clarity
- [ ] No `[NEEDS CLARIFICATION]` markers remaining
- [ ] Every requirement is testable (not vague or aspirational)
- [ ] No ambiguous terms without definition
- [ ] Examples included for complex or ambiguous behaviors
- [ ] Input/output pairs provided for data transformations

**Why**: Ambiguity is the leading cause of "correct but wrong" output. The AI will resolve ambiguity silently, often incorrectly.
**How**: Search the spec for subjective terms ("fast", "user-friendly", "secure"). Replace each with a measurable criterion:
```
Bad:  "The API should be fast"
Good: "The API responds in <200ms at p95 for 1000 concurrent users"
```

### Constraints
- [ ] Coding standards specified (or referenced via `CLAUDE.md` / `.cursorrules`)
- [ ] Framework and library constraints stated
- [ ] "Never do" rules explicit
- [ ] Security requirements included where applicable
- [ ] Performance budgets stated where applicable

**Why**: Without constraints, the AI picks the most statistically likely approach from its training data, which may not match your stack or standards.
**How**: Add a Constraints section to the spec. At minimum, state: language version, framework, prohibited patterns, and any libraries that must or must not be used.

### Structure
- [ ] Spec fits within context window (<8K tokens for detailed, <2K for high-level)
- [ ] Interfaces explicitly defined (type signatures, API schemas)
- [ ] Dependencies identified and ordered
- [ ] Tasks decomposed into focused units (<200 LOC each)

**Why**: Oversized specs cause context window truncation and attention degradation. The AI performs best on focused, well-bounded tasks.
**How**: Check token count before generation:
```bash
# Rough token estimate (1 token ~ 4 characters)
wc -c spec.md | awk '{printf "~%d tokens\n", $1/4}'
```
If over 8K tokens, split into a high-level spec and per-component detail specs. Feed the high-level spec first, then each detail spec in dependency order.

### Verification
- [ ] Test criteria included for every requirement
- [ ] Acceptance scenarios defined
- [ ] Definition of done is unambiguous

**Why**: Without verification criteria baked into the spec, post-generation review becomes subjective and inconsistent.
**How**: For each functional requirement, add a "Verify:" line:
```
Requirement: User can reset password via email link
Verify: Sending POST /reset with valid email returns 200 and sends email within 5s
Verify: Link expires after 24 hours
Verify: Used link returns 410 Gone
```

## Red Flags

If any of these are true, the spec needs work before generation:

- Contains phrases like "make it good" or "user-friendly" without concrete criteria
- No out-of-scope section (scope will creep during generation)
- Spec exceeds 8K tokens without layering into sub-specs
- No test criteria for any requirement
- Interface contracts are implied rather than explicit
- Multiple unrelated features in a single spec (split into focused specs)

## Quick Reference

| Check | Pass Criteria |
|-------|--------------|
| Goal | 1-2 sentences, answers "what" and "why" |
| Scope | In-scope and out-of-scope both stated |
| Requirements | Each is independently testable |
| Constraints | Language, framework, standards, prohibitions |
| Interfaces | Type signatures or API schemas, not prose |
| Size | <8K tokens (detailed) or <2K tokens (high-level) |
| Verification | Every requirement has at least one "Verify:" line |

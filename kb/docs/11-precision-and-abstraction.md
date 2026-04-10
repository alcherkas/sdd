# Precision and Abstraction: Why Code Survives AI

> Last verified: 2026-04-10

---

## Principle

AI does not eliminate the need for precise abstractions — it amplifies it. Vibe coding creates the illusion that informal vibes are precise abstractions, but this illusion collapses at scale. The fundamental work of programming is theory-building and mastering complexity through precise abstractions, not producing lines of code.

---

## The Precision Argument

Steve Krouse (creator of Val Town) argues in "Reports of code's death are greatly exaggerated" that the death of code is premature. His central claim: vibe coding feels like it works until it doesn't, and the failure mode is the same one that plagues all legacy code — no one understands what it does or why.

### Vibe Coding as Legacy Code

Andrej Karpathy coined "vibe coding" to describe a practice where developers "forget that the code even exists." Krouse draws a direct parallel: **we already have a phrase for code nobody understands — legacy code.** Legacy code is universally despised because it resists modification, hides unexpected behaviors, and fails in ways that are difficult to diagnose.

> "Vibe coding gives the illusion that your vibes are precise abstractions. They will feel this way right up until they leak, which will happen when you add enough features or get enough scale. Unexpected behaviors (bugs) that emerge from lower levels of abstraction that you don't understand will sneak up on you and wreck your whole day."
> — Steve Krouse [1]

### Programming as Theory Building

Krouse references Peter Naur's classic argument: programming is fundamentally **theory building**, not producing lines of code. The value of a programmer is not in the text they produce but in the mental model they hold of how the system works. Code is the artifact that encodes this theory into a form machines can execute.

This has direct implications for AI-assisted development:

| Approach | Theory Building | Outcome |
|----------|----------------|---------|
| Vibe coding | No theory built — developer defers to AI | Fragile code that breaks under modification |
| Spec-driven development | Theory encoded in specifications | Durable code anchored to explicit intent |
| Direct coding with AI | Theory built through iterative prompting | Variable — depends on developer engagement |

### Why Abstractions Still Matter

Krouse argues that even with AGI, the first things we will use it for are our hardest abstraction problems — to help us make **better** abstractions so that we can better understand and master complexity. AI does not remove the need for abstraction; it raises the ceiling on what abstractions we can build.

This is the key insight for SDD: specifications are the abstraction layer between human intent and AI-generated code. Without that layer, there is no theory, no understanding, and no ability to maintain the system.

---

## The Spec-as-Code Argument

Gabriella Gonzalez (author of the Haskell "turtle" and "dhall" libraries) offers a complementary and more skeptical perspective in "A sufficiently detailed spec is code." Her argument: if a specification is precise enough to generate working code, it is no longer a specification — it is code by another name [2].

### The Symphony Experiment

Gonzalez attempted to use Claude Code to build the Symphony project (an agent orchestrator) in Haskell. The project provided a SPEC.md file intended to serve as the specification. Gonzalez's findings:

1. **The spec was pseudocode.** SPEC.md read less like a requirements document and more like pseudocode in markdown form.
2. **Bugs required manual intervention.** Multiple bugs emerged that required Gonzalez to prompt Claude to fix them.
3. **Silent failures.** Even when there were no error messages, the agent would spin without making progress.

### The Core Claim

> "If you try to make a specification document precise enough to reliably generate a working implementation, you must necessarily contort the document into code or something strongly resembling code."
> — Gabriella Gonzalez [2]

Gonzalez argues this creates a fundamental tension:

| Spec Precision | Consequence |
|----------------|-------------|
| **Low** (vague natural language) | AI generates plausible but incorrect code; high error rate |
| **Medium** (structured requirements) | AI generates better code but gaps remain in edge cases |
| **High** (fully precise) | The spec itself becomes code — no simplification achieved |

### Engineering Labor Cannot Be Disguised

Gonzalez's strongest claim: engineering labor cannot be eliminated through vague language — it can only be disguised. If an agent requires a specification to function, that document must contain the same level of detail as the code itself. The complexity is conserved; it merely moves from one artifact to another.

> "Agentic coders are learning the hard way that you can't escape the 'narrow interfaces' (read: code) that engineering labor requires; you can only transmute that labor into something superficially different which still demands the same precision."
> — Gabriella Gonzalez [2]

---

## Implications for SDD

These two perspectives — Krouse's defense of precision and Gonzalez's skepticism about spec simplification — converge on a shared insight that matters for SDD practitioners:

### What Both Arguments Agree On

1. **Precision is non-negotiable.** Whether encoded in code or in specifications, the precision required to build working software cannot be reduced.
2. **Vibe coding fails at scale.** Informal prompting without structural guidance produces legacy-quality output.
3. **Abstraction is the real work.** The hard work of software development is building mental models and precise abstractions, not typing syntax.

### Where They Diverge — and What SDD Should Learn

| Question | Krouse | Gonzalez | SDD Implication |
|----------|--------|----------|-----------------|
| Can specs replace code? | No — but specs + code > code alone | No — detailed specs become code | Specs are a complement, not a replacement for code |
| Is spec writing easier than coding? | Different skill, same rigor | No — same complexity, different syntax | Don't promise spec writing is "easier" — promise it is more reviewable and maintainable |
| Where does the value of SDD come from? | Precision and theory building | Not from avoiding code | Value comes from making intent explicit, reviewable, and version-controlled |

### The Practical Takeaway

SDD does not promise to eliminate complexity. It promises to **relocate complexity** from opaque generated code into reviewable, version-controlled specifications. The spec is not simpler than the code — but it is more legible to humans, more amenable to review, and more stable as a maintenance target.

The anti-pattern to avoid: marketing SDD as "you don't need to code anymore." The accurate framing: SDD ensures that the hard thinking happens before generation, in a format humans can review, rather than after generation, when debugging opaque AI output.

---

## References

1. Krouse, S. "Reports of code's death are greatly exaggerated." 2026.
   https://stevekrouse.com/precision

2. Gonzalez, G. "A sufficiently detailed spec is code." Haskell for All, March 2026.
   https://haskellforall.com/2026/03/a-sufficiently-detailed-spec-is-code

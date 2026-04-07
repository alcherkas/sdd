# Anatomy of a Well-Formed Spec

Last verified: 2026-04-04

A well-formed specification is not a flat document -- it is a directed graph of interdependent sections. The goal statement feeds into requirements, requirements drive contracts and test criteria, and an explicit "out of scope" section bounds everything. Understanding this structure helps you write specs that are complete, consistent, and verifiable by both humans and AI.

## Mermaid Diagram

```mermaid
graph TD
    GOAL["Goal Statement"]
    UCTX["User Context"]
    FR["Functional<br/>Requirements"]
    NFR["Non-Functional<br/>Requirements"]
    IC["Interface<br/>Contracts"]
    TC["Test Criteria"]
    CON["Constraints"]
    PB["Performance<br/>Budgets"]
    DM["Data Models"]
    VP["Verification<br/>Plan"]
    OOS["Out of Scope"]

    %% Goal feeds into requirements
    GOAL -- "feeds into" --> FR
    GOAL -- "feeds into" --> NFR

    %% User Context informs Functional Requirements
    UCTX -- "informs" --> FR

    %% Functional Requirements drive contracts and tests
    FR -- "drives" --> IC
    FR -- "drives" --> TC

    %% Non-Functional Requirements drive constraints and budgets
    NFR -- "drives" --> CON
    NFR -- "drives" --> PB

    %% Interface Contracts define Data Models
    IC -- "defines" --> DM

    %% Test Criteria and Constraints combine into Verification Plan
    TC -- "combines into" --> VP
    CON -- "combines into" --> VP

    %% Out of Scope bounds everything
    OOS -. "bounds" .-> GOAL
    OOS -. "bounds" .-> FR
    OOS -. "bounds" .-> NFR
    OOS -. "bounds" .-> IC
    OOS -. "bounds" .-> TC
    OOS -. "bounds" .-> CON

    %% Styling
    classDef goalNode fill:#1a3a5c,stroke:#0d2137,stroke-width:3px,color:#ffffff,font-size:14px
    classDef reqNode fill:#2d4a3e,stroke:#1b3029,stroke-width:2px,color:#ffffff
    classDef contractNode fill:#3a2d4a,stroke:#251b3a,stroke-width:2px,color:#ffffff
    classDef verifyNode fill:#4a2d2d,stroke:#3a1b1b,stroke-width:2px,color:#ffffff
    classDef scopeNode fill:#4a4a2d,stroke:#3a3a1b,stroke-width:2px,color:#ffffff

    class GOAL,UCTX goalNode
    class FR,NFR reqNode
    class IC,DM,CON,PB contractNode
    class TC,VP verifyNode
    class OOS scopeNode

    %% Link styling for "bounds" edges (dashed)
    linkStyle 10,11,12,13,14,15 stroke:#8b8b00,stroke-width:1px,stroke-dasharray:4 4
```

## ASCII Fallback

For terminals and environments that do not render Mermaid:

```
                     +-----------------+         +----------------+
                     |  Goal Statement |         |  User Context  |
                     +--------+--------+         +-------+--------+
                              |                          |
                  +-----------+-----------+              |
                  |                       |    informs   |
                  v                       v              |
         +--------+--------+    +---------+---------+    |
         |   Functional    |<---+   Non-Functional  |    |
         |  Requirements   |    |   Requirements    |    |
         +---+--------+----+    +----+----------+---+    |
             |        |              |          |        |
          drives    drives        drives     drives      |
             |        |              |          |        |
             v        v              v          v        |
        +----+---+ +--+-------+ +---+------+ +-+--------+-+
        |Interface| |  Test    | |Constraints| |Performance|
        |Contracts| | Criteria | |          | |  Budgets  |
        +----+----+ +----+----+ +-----+----+ +-----------+
             |            |           |
          defines     combines     combines
             |          into        into
             v            |           |
        +----+----+       v           |
        |  Data   |  +---+-----------+--+
        | Models  |  | Verification     |
        +---------+  |     Plan         |
                     +------------------+

    . . . . . . . . . . . . . . . . . . . . . . . . . . .
    :                  Out of Scope                       :
    :   (bounds all sections -- defines what is NOT       :
    :    part of this spec)                               :
    . . . . . . . . . . . . . . . . . . . . . . . . . . .
```

## Section Reference Table

| Section | Purpose | Required? | Typical Size |
|---------|---------|-----------|--------------|
| **Goal Statement** | What and why in 1-2 sentences | Yes | 50-100 words |
| **User Context** | Who needs this and their situation | Yes | 50-150 words |
| **Functional Requirements** | What the system must do | Yes | 200-800 words |
| **Non-Functional Requirements** | Performance, security, accessibility | Recommended | 100-300 words |
| **Success Criteria** | Testable acceptance conditions | Yes | 100-300 words |
| **Interface Contracts** | API schemas, type signatures | For multi-component | 200-500 words |
| **Constraints** | Coding standards, "never do" rules | Recommended | 100-300 words |
| **Out of Scope** | Explicit boundaries | Yes | 50-150 words |
| **Open Questions** | Unresolved [NEEDS CLARIFICATION] items | As needed | Variable |

## Quality Checklist

Before feeding a spec to an AI agent, validate it against these questions:

- [ ] **Is every requirement testable?** Each functional requirement should map to at least one concrete pass/fail criterion. If you cannot describe how to test it, the requirement is too vague.
- [ ] **Are interfaces explicitly defined?** Every boundary between components -- API endpoints, function signatures, data shapes -- should have an unambiguous contract. Do not leave the AI to infer schemas.
- [ ] **Is scope bounded?** The "Out of Scope" section must exist and explicitly name what this spec does *not* cover. Unbounded specs invite unbounded implementations.
- [ ] **Does it fit in <8K tokens?** A spec that exceeds a single context window forces the AI to work from partial information. If it is too long, decompose it into smaller specs with clear interface contracts between them.
- [ ] **Are examples included for ambiguous behaviors?** Wherever a requirement could be interpreted multiple ways, provide a concrete input/output example that resolves the ambiguity.
- [ ] **Are "never do" rules explicit?** Critical prohibitions (e.g., "never expose raw SQL errors to the client", "never mutate global state") must be stated directly. AI agents treat silence as permission.

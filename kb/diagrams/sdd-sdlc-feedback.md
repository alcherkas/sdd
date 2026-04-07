# SDD-SDLC Feedback Loop Model

Last verified: 2026-04-04

## Overview

SDD transforms the traditional SDLC by placing the specification at the center as a living source of truth, with continuous feedback loops at every phase replacing traditional end-of-phase gates. Instead of a linear waterfall where each phase hands off to the next, every phase reads from and writes back to the specification. Drift is detected early, corrections are cheap, and the spec always reflects ground truth.

## Mermaid Diagram

```mermaid
graph TD
    SPEC["SPECIFICATION<br/><i>Living Source of Truth</i>"]

    REQ["1. Requirements<br/><b>SPECIFY</b>"]
    DES["2. Design<br/><b>PLAN</b>"]
    TSK["3. Task Breakdown<br/><b>DECOMPOSE</b>"]
    IMP["4. Implementation<br/><b>GENERATE</b>"]
    TST["5. Testing<br/><b>VERIFY</b>"]
    DEP["6. Deployment<br/><b>RELEASE</b>"]
    MNT["7. Maintenance<br/><b>EVOLVE</b>"]

    %% Sequential phase flow
    REQ --> DES
    DES --> TSK
    TSK --> IMP
    IMP --> TST
    TST --> DEP
    DEP --> MNT

    %% Loop back from Maintenance to Requirements
    MNT -- "spec update &<br/>regeneration" --> REQ

    %% Bidirectional feedback between each phase and the specification
    REQ <-- "clarification questions,<br/>research findings" --> SPEC
    DES <-- "constitution gates,<br/>consistency validation" --> SPEC
    TSK <-- "dependency validation,<br/>parallelization analysis" --> SPEC
    IMP <-- "drift detection,<br/>test failures" --> SPEC
    TST <-- "coverage gaps,<br/>acceptance validation" --> SPEC
    DEP <-- "operational metrics,<br/>SRE monitoring" --> SPEC
    MNT <-- "incidents,<br/>harness strengthening" --> SPEC

    %% Styling
    classDef specNode fill:#1a3a5c,stroke:#0d2137,stroke-width:3px,color:#ffffff,font-size:16px
    classDef phaseNode fill:#2d4a3e,stroke:#1b3029,stroke-width:2px,color:#ffffff
    classDef default font-family:sans-serif

    class SPEC specNode
    class REQ,DES,TSK,IMP,TST,DEP,MNT phaseNode

    %% Link styling
    linkStyle 0,1,2,3,4,5 stroke:#4a6a5a,stroke-width:2px
    linkStyle 6 stroke:#8b4513,stroke-width:2px,stroke-dasharray:5 5
    linkStyle 7,8,9,10,11,12,13 stroke:#1a3a5c,stroke-width:1.5px
```

## ASCII Art Fallback

```
                        +--------------------------------------+
                        |          SPECIFICATION               |
                        |     (Living Source of Truth)         |
                        +--------------------------------------+
                          |    |    |    |    |    |    |
       clarification -----+    |    |    |    |    |    +----- incidents,
       questions,          |    |    |    |    |    |          harness
       research findings   |    |    |    |    |    |          strengthening
                           |    |    |    |    |    |
  +-------------+          |    |    |    |    |    |      +-------------+
  | 1. Require- |<---------+    |    |    |    |    +----->| 7. Mainten- |
  |    ments    |               |    |    |    |           |    ance     |
  |   SPECIFY   |               |    |    |    |           |   EVOLVE   |
  +------+------+               |    |    |    |           +------+------+
         |                      |    |    |    |                  ^
         v         constitution |    |    |    | operational      |
  +-------------+  gates,       |    |    |    | metrics,   +-----+-------+
  | 2. Design   |<--------------+    |    |    +----------->| 6. Deploy-  |
  |    PLAN     |                    |    |                 |    ment     |
  +------+------+                    |    |                 |   RELEASE  |
         |            dependency     |    |   coverage      +------+------+
         v            validation,    |    |   gaps,                ^
  +-------------+     paralleliz.    |    |   acceptance    +------+------+
  | 3. Task     |<-------------------+    +---------------->| 5. Testing  |
  |  Breakdown  |                    |                      |   VERIFY   |
  |  DECOMPOSE  |                    |                      +------+------+
  +------+------+       drift        |                             ^
         |              detection,   |                             |
         v              test         |                             |
  +-------------+       failures     |                      +------+------+
  | 4. Implement|<-------------------+                      |             |
  |   GENERATE  |---------------------------------------------->----------+
  +-------------+

  Maintenance ----( spec update & regeneration )----> Requirements
```

## Summary Table

| SDLC Phase | SDD Activity | Feedback Mechanism | What Flows Back to Spec |
|---|---|---|---|
| **Requirements** | SPECIFY | Clarification questions, research findings | Ambiguity resolutions, domain constraints, edge cases discovered during elicitation |
| **Design** | PLAN | Constitution gates, consistency validation | Architectural decisions, interface contracts, cross-cutting concern resolutions |
| **Task Breakdown** | DECOMPOSE | Dependency validation, parallelization analysis | Task granularity adjustments, sequencing constraints, shared-resource conflicts |
| **Implementation** | GENERATE | Drift detection, test failures | API shape corrections, behavioral clarifications, constraint refinements |
| **Testing** | VERIFY | Coverage gaps, acceptance validation | Missing acceptance criteria, untested invariants, boundary conditions |
| **Deployment** | RELEASE | Operational metrics, SRE monitoring | Performance baselines, resource requirements, rollback conditions |
| **Maintenance** | EVOLVE | Incidents, harness strengthening | Bug-driven spec patches, new regression tests, operational hardening rules |

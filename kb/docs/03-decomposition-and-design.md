# Phase 2: Decomposition and Design

> Last verified: 2026-04-04

---

## Principle

Break requirements into implementation-ready units with explicit interfaces,
data models, and dependency relationships. The decomposition determines the
scope of each AI generation session. Get the decomposition wrong and every
downstream generation session inherits the error.

---

## 1. Why Decomposition Matters for AI

LLMs perform measurably better on focused, bounded tasks than on broad,
cross-cutting ones. Empirical evidence from code generation benchmarks shows
that success rates drop as the number of files, interfaces, and concerns in a
single prompt increases [1][2].

| Property | Effect on AI Generation |
|---|---|
| **Focused scope per session** | Each generation session targets a single component with a clear input/output contract. The agent does not need to hold the full system in context. |
| **Explicit interface contracts** | Cross-component consistency is enforced by contracts, not by the agent's ability to remember prior sessions. Contracts are loaded into context; memory is not. |
| **Dependency ordering** | The generation sequence follows the dependency graph. Foundational components are generated first, so dependent components can reference real (not hallucinated) interfaces. |
| **Smaller verification surface** | Each generated unit can be tested against its contract in isolation before integration. Defects are localized to a single component. |

Without decomposition, the developer is asking the AI to simultaneously make
architectural decisions, design interfaces, and implement logic. This exceeds
what current models do reliably in a single pass [1].

---

## 2. Decomposition Strategies

### Component-Based Decomposition

Identify discrete modules with clear responsibilities. Each component should
satisfy the Single Responsibility Principle: one reason to change, one owner,
one generation session [3].

**How to identify components:**

1. List the nouns in your requirements -- these are candidate entities and
   modules.
2. Group related behaviors around those nouns.
3. Draw boundaries where data ownership changes (who reads vs. who writes).
4. Validate that each component can be described in one sentence. If you need
   a compound sentence, the component is too large.

| Indicator | What It Means |
|---|---|
| Component description requires "and" | Split into two components |
| Two components always change together | Merge into one component |
| Component has more than 3-4 dependencies | Consider splitting or introducing a facade |
| Component cannot be tested without its dependents | Interface boundary is missing or too leaky |

### Interface-First Design

Define contracts before internals. The contract is the spec for the generation
session; the internals are what the agent produces [1][3].

**What to define for each interface:**

- **Type signatures.** Input types, output types, generic constraints. Use the
  project's type system (TypeScript interfaces, Python protocols, Go interfaces,
  Rust traits).
- **Pre-conditions.** What must be true before the function/endpoint is called.
  Express as assertions or validation rules.
- **Post-conditions.** What is guaranteed to be true after successful execution.
  Express as return type guarantees and state changes.
- **Error handling contract.** What errors can occur, how they are represented,
  and who is responsible for handling them (caller vs. callee).

### Data Model Specification

Data models are the most frequently under-specified element in AI-generated
code. Without explicit models, agents invent schemas that are internally
consistent but incompatible with the rest of the system [2].

**What to specify:**

| Element | What to Define | Example |
|---|---|---|
| **Entities** | Fields, types, required vs. optional | `Task { id: UUID, title: string, status: enum }` |
| **Relationships** | Cardinality, ownership, cascade behavior | `Board 1:N Column, Column 1:N Task` |
| **Validation rules** | Constraints beyond type | `title: 1-200 chars, non-empty` |
| **State transitions** | Valid transitions, guards | `draft -> active -> completed` (no skip) |
| **Indexes and access patterns** | How data is queried | "Tasks are queried by board_id and status" |

### Task Breakdown

Convert the decomposition into an ordered implementation plan. Each task maps
to one generation session.

**Rules for task breakdown:**

1. Each task produces a testable artifact (a module, an endpoint, a component).
2. Each task lists its input dependencies explicitly.
3. Tasks are ordered so that no task depends on an artifact that has not yet
   been generated.
4. Each task includes its verification criterion (how to know it is done).

---

## 3. Interface Contracts

An interface contract is the most critical artifact of the decomposition phase.
It serves as both the spec for the generation session and the integration test
for the generated code.

### How to Write Them

```typescript
/**
 * Moves a task from one column to another within the same board.
 *
 * Pre-conditions:
 * - taskId references an existing task
 * - targetColumnId references a column in the same board as the task
 * - position is a non-negative integer <= target column's task count
 *
 * Post-conditions:
 * - Task belongs to targetColumnId
 * - Task is at the specified position; other tasks shift accordingly
 * - Task's updatedAt timestamp is refreshed
 *
 * Errors:
 * - TaskNotFound (404): taskId does not exist
 * - ColumnNotFound (404): targetColumnId does not exist
 * - CrossBoardMove (400): task and target column are on different boards
 * - InvalidPosition (400): position exceeds column task count
 */
function moveTask(
  taskId: UUID,
  targetColumnId: UUID,
  position: number
): Promise<Task>;
```

### Contract Elements

| Contract Element | What It Specifies | Example |
|---|---|---|
| **Type signature** | Input/output types, generic constraints | `(taskId: UUID, targetColumnId: UUID, position: number) -> Promise<Task>` |
| **Pre-conditions** | What must be true before invocation | "taskId must reference an existing task in the database" |
| **Post-conditions** | What is guaranteed after success | "Task.columnId equals targetColumnId; Task.updatedAt is refreshed" |
| **Error contract** | Failure modes and their representations | `TaskNotFound -> 404`, `CrossBoardMove -> 400` |
| **Side effects** | State changes beyond the return value | "Other tasks in the target column shift position by +1" |
| **Idempotency** | Whether repeated calls produce the same result | "Moving a task to its current position and column is a no-op" |

### Schema-Based Contracts

For API boundaries, use formal schema languages:

- **OpenAPI 3.1** for REST endpoints -- defines paths, request/response schemas,
  error responses, and authentication requirements in a machine-readable format.
- **GraphQL SDL** for GraphQL APIs -- defines types, queries, mutations, and
  subscriptions with built-in nullability and type validation.
- **JSON Schema** for data validation -- defines the shape of data at rest or
  in transit, including constraints like `minLength`, `pattern`, and `enum`.
- **Protocol Buffers** for gRPC services -- defines service interfaces and
  message types with strong typing and backward-compatibility guarantees.

These schemas serve double duty: they are both the spec for the AI agent and the
runtime validation layer for the generated code [3].

---

## 4. Delta Specs for Existing Codebases

Most real-world development is brownfield -- modifying existing systems rather
than building new ones. Writing a full specification for every change in an
existing codebase is wasteful. Delta specs describe **what is changing**, not
everything [4].

### The ADDED / MODIFIED / REMOVED Pattern

The OpenSpec framework formalizes this with explicit change markers [4]:

```markdown
## Changes

### ADDED: Rate Limiting Middleware
- New middleware applied to all /api/* routes
- Uses token bucket algorithm (100 req/min per API key)
- Returns 429 with Retry-After header when exceeded

### MODIFIED: POST /api/tasks
- Add rate limiting headers to response (X-RateLimit-Remaining,
  X-RateLimit-Reset)
- No changes to request schema or business logic

### REMOVED: Legacy /v1/tasks endpoint
- Deprecated in v2.3, removing in v3.0
- Clients must migrate to /api/tasks
```

### When to Use Delta Specs

| Scenario | Full Spec | Delta Spec |
|---|---|---|
| Greenfield project | Yes | No -- nothing to delta from |
| Adding a feature to an existing module | No | Yes -- describe what changes |
| Refactoring internals without API change | No | Yes -- describe structural changes, assert no behavior change |
| Major architectural migration | Yes | No -- scope is too broad for deltas |
| Bug fix in existing code | No | Yes -- the bug fix spec (from Phase 1) is inherently a delta |

Delta specs are effective because they match the agent's actual task: the agent
is not building the system from scratch; it is modifying specific parts. Giving
it a full system spec wastes context window budget and introduces noise [4].

---

## 5. Generation Sequence Planning

The order in which components are generated matters. Generating a dependent
component before its dependency forces the agent to hallucinate the dependency's
interface. When the dependency is later generated, its actual interface may
differ, creating integration failures.

### Ordering Rules

1. **Data models first.** Entity definitions, database schemas, and type
   definitions are the foundation. Everything else references them.
2. **Shared utilities and helpers second.** Validation functions, formatters,
   and common abstractions that multiple components depend on.
3. **Core business logic third.** Service layers, domain logic, and state
   machines that implement the primary behavior.
4. **API/interface layers fourth.** REST endpoints, GraphQL resolvers, or CLI
   handlers that expose business logic to consumers.
5. **UI components last.** Views and components that consume API layers and
   render data.

### Example: Kanban Board Feature

| Order | Component | Dependencies | Why This Order |
|---|---|---|---|
| 1 | Data models (Task, Column, Board) | None | All other components reference these types |
| 2 | Database migration and repository layer | Data models | CRUD operations define the persistence contract |
| 3 | Task service (move, create, archive) | Data models, repository | Business rules depend on persistence and types |
| 4 | REST API endpoints | Task service, data models | Endpoints are a thin layer over the service |
| 5 | WebSocket event broadcasting | Task service | Real-time updates subscribe to service events |
| 6 | Kanban board UI component | REST API, WebSocket, data models | UI renders data from API and reacts to events |
| 7 | Drag-and-drop interaction handler | Kanban board UI, REST API | Interaction logic depends on the UI component structure and API contract |

Each row is one generation session. The agent receives the component's interface
contract plus the already-generated contracts (not implementations) of its
dependencies. This gives the agent enough context to generate correct code
without overwhelming the context window.

### Validating the Sequence

Before starting generation, verify the sequence against these checks:

| Check | How to Verify |
|---|---|
| **No forward references** | Every dependency listed in column 3 appears at a lower order number |
| **No orphaned components** | Every component is either a dependency of another or a leaf node (UI, CLI) |
| **Testable in isolation** | Each component at order N can be unit-tested using only components at order < N |
| **Parallelizable where possible** | Components at the same order with no mutual dependencies can be generated concurrently |

---

## References

1. Osmani, A. "How to Write a Good Spec." O'Reilly, 2025.
   https://addyosmani.com/blog/good-spec/

2. Thoughtworks. "Spec-Driven Development." December 2025.
   https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices

3. GitHub. "Spec-Driven Development with AI." 2026.
   https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

4. Fission-AI. "OpenSpec Framework." 2025-2026.
   https://github.com/Fission-AI/OpenSpec

5. Fowler, M. "Understanding SDD -- Kiro, spec-kit, Tessl." 2026.
   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

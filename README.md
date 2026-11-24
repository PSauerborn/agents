# agents

A reusable set of [Claude Code](https://claude.com/claude-code) agent definitions, skills, and orchestration procedures that turn a spec or a plain-language request into a validated, reviewed changeset through a coordinated team of specialist subagents.

Two entry points share the same agents:

- **`/draft-spec <request>`** — start from your own words. A spec is drafted from your request for you to approve, then its acceptance criteria are drafted with you one scenario at a time, updating the existing acceptance suite where the spec changes behaviour. It stops there; nothing is implemented. This is the spec-kit style path: you never have to write the spec yourself.
- **`/implement-spec <spec-path>`** — start from a spec drafted with `/draft-spec` or written with `/init-spec`. Review and clarify, analyse, optionally design, plan, decompose, execute, review, validate acceptance.

Orchestrators never edit code and never commit — subagents implement, and committing is left to you. Each subagent owns one responsibility, carries only the context its role requires, and returns a structured JSON response (`completed` or `blocked` with a typed reason). Shared skills define the conventions — orchestration rules, canonical artifact locations, coding standards, the approval protocol — so every run is repeatable.

## Quickstart

Install as a plugin:

```bash
git clone https://github.com/PSauerborn/agents.git
claude --plugin-dir ./agents
```

Or copy the definitions into a project's `.claude/` (or `~/.claude/` for global use):

```bash
cp -r agents skills /path/to/your-project/.claude/
```

Set `CODING_STANDARDS_DIR` to your standards repository — the `coding-standards` skill requires it. Then:

```text
/draft-spec add CSV export to the orders page            # drafts a spec + acceptance criteria with you, then stops
/init-spec <feature description>                         # or scaffold a spec yourself
/implement-spec docs/specs/SPEC-NNN.md                   # run either kind of spec
```

(Namespaced as `/subagents-dev:<skill>` when installed as a plugin.) `/review-spec <spec-path>` runs the review and clarification gate on its own.

## The Flow

```mermaid
flowchart TD
    REQUEST([request]) --> ANALYZE["requirements-analyzer<br/>files · UI impact · questions"]
    ANALYZE --> WRITE["spec-writer<br/>draft spec, gaps marked"] --> SAPPROVE["spec approval"] --> ACS["draft-spec-ac<br/>acceptance criteria, one at a time"] --> SPEC

    SPEC([spec]) --> GATE["review-spec<br/>review + clarifications"]
    GATE --> REQ["requirements-analyzer"]
    REQ -.->|"significant UI"| DESIGN["frontend-designer<br/>3 options"] --> SELECT["design selection"] -.-> PLAN
    REQ --> PLAN["work-planner"]
    PLAN --> APPROVE["plan approval"]
    APPROVE --> DECOMP["task-decomposer<br/>coverage-checked task files"]
    DECOMP --> EXEC["task-executor<br/>TDD · parallel only when safe"]
    EXEC --> REVIEW

    subgraph REVIEW["review stage — one remediation loop, max 2 iterations"]
        direction LR
        VAL["validation-runner"]
        CR["code-reviewer<br/>correctness + standards"]
        SEC["security-reviewer"]
        ACC["acceptance-validator"]
    end

    REVIEW -->|"remediation tasks"| EXEC
    REVIEW -->|"still failing"| ESCALATE["escalate to user"]
    REVIEW -->|"clean"| DONE([changeset ready to commit])

    classDef stop fill:#fde4e4,stroke:#d64545,color:#10203f
    classDef terminal fill:#e6f6ec,stroke:#2f9e5e,color:#0f3a22
    class GATE,APPROVE,SELECT,ESCALATE,SAPPROVE,ACS stop
    class SPEC,DONE,REQUEST terminal
```

Red = `[STOP]` checkpoints where the orchestrator waits for you · dashed = conditional.

There is one flow. `/draft-spec` covers the top row and ends with the spec and its acceptance criteria written; `/implement-spec` picks up from any spec, drafted or written by you, and runs every stage.

Key mechanics:

- **Specify, don't guess** — a drafted spec marks every unknown as `[NEEDS CLARIFICATION]` instead of inventing a requirement, and you approve the draft before anything is planned.
- **Clarify, don't guess** — the spec review asks the marked questions first, then a bounded set of further one-at-a-time questions (five per feature), and records your answers in the review document; the spec itself is never edited by an agent once approved.
- **Execution manifest** — the orchestrator maintains one manifest of everything the executors changed; every reviewer works from it.
- **One remediation loop** — every reviewer returns the same `findings / remediationRequired / remediationTaskPath` shape; executors apply remediation tasks; the flagging reviewer re-verifies. After 2 iterations with outstanding findings the run stops and escalates.
- **Coverage before execution** — the decomposer blocks if any acceptance criterion has no task or any task has no criterion.
- **Parallel execution guard** — tasks run in parallel only when they have no dependency relationship and disjoint write sets.
- **Blocked, not improvised** — any agent missing a required input returns `blocked` with a typed reason.

## Artifacts

Everything lives under `docs/` in the target repo:

```text
docs/specs/SPEC-NNN.md, SPEC-NNN-REVIEW.md
docs/designs/DES-NNN/
docs/plans/WP-NNN/work-plan.md, manifest.md, tasks/
```

## Agents

| Agent | Responsibility |
| --- | --- |
| `requirements-analyzer` | Assess task type, UI impact, write set, read set, and open questions |
| `spec-writer` | Draft a spec from a plain-language request, cut to its MVP, marking every unknown for clarification |
| `frontend-designer` | Produce 3 distinct design options (doc + HTML mockup) for significant UI changes |
| `work-planner` | Convert a spec, its review, and the requirements summary into a work plan |
| `task-decomposer` | Split the work plan into single-commit task files; verify criterion coverage |
| `task-executor` | Implement exactly one task file (TDD) |
| `validation-runner` | Run the project's full build/test/lint suite; verdict `verified / partial / failed` |
| `code-reviewer` | Peer-review the changeset for correctness, edge cases, design, and standards |
| `security-reviewer` | Review the changeset for any exploitable weakness and dependency risk |
| `acceptance-validator` | Verify every spec acceptance criterion is demonstrably met |

Response schemas live in `skills/subagents-orchestration-guide/reference/responses/`; artifact templates and canonical paths in `skills/documentation-criteria/`.

## Development

`make scan-secrets` runs a [detect-secrets](https://github.com/Yelp/detect-secrets) scan; [pre-commit](https://pre-commit.com) hooks (`pre-commit install`) enforce file hygiene, Markdown linting, and secret scanning.

## License

See [LICENSE](LICENSE).

# Usage Report: [Work Plan ID]

Work Plan ID: WP-[0-9]{3}
Created Date: YYYY-MM-DD

## Agents Used

One row per agent invocation. The same agent may run multiple times (e.g. the
`task-executor` typically runs once per task), so each run gets its own row and
is numbered in the `Run` column. Use the `Phase` column to attribute each run to
one of `planning`, `execution`, or `review`.

| Agent | Phase | Run | Duration | Tokens |
| ----- | ----- | --- | -------- | ------ |
| [agent-name] | planning \| execution \| review | [n] | [HH:MM:SS] | [count] |
| | | | **Total** | **[sum]** |

## Token Usage

### By Agent

Token totals aggregated across all runs of each agent.

| Agent | Runs | Total Duration | Total Tokens |
| ----- | ---- | -------------- | ------------ |
| [agent-name] | [count] | [HH:MM:SS] | [count] |
| **Total** | **[sum]** | **[sum]** | **[sum]** |

### By Phase

Token totals aggregated across all agents that ran in each phase.

| Phase | Agents | Total Duration | Total Tokens |
| --------- | ------ | -------------- | ------------ |
| planning | [list of agents] | [HH:MM:SS] | [count] |
| execution | [list of agents] | [HH:MM:SS] | [count] |
| review | [list of agents] | [HH:MM:SS] | [count] |
| **Total** | | **[sum]** | **[sum]** |

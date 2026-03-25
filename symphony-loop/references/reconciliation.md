# Reconciliation Protocol

How the orchestration loop detects and responds to external state changes,
manages retries, and cleans up stale work.

## Poll-Tick Reconciliation

On every poll tick, before dispatching new work:

1. **Fetch current state** for all issues with active work (running or retry-queued).
2. For each tracked issue:
   - **Terminal state** (Done, Closed, Cancelled, Duplicate):
     - Stop any running work immediately
     - Clean up workspace
     - Release the claim
   - **Still active** (in `active_states`):
     - Update in-memory state snapshot
     - Continue running work
   - **Unknown state** (not in active or terminal):
     - Stop running work
     - Keep workspace (may need investigation)
     - Release the claim
3. **Stall detection**: for running issues, check if `elapsed_since_last_activity > stall_timeout_ms`.
   - If stalled: terminate the agent process and schedule a retry.
   - Default stall timeout: 300,000ms (5 minutes).
   - If stall timeout is `<= 0`, stall detection is disabled.

## Retry and Backoff

When an execution attempt fails (agent error, timeout, stall):

### Continuation Retry (prior session succeeded but issue still active)

- Fixed delay: **1,000ms**
- The `attempt` variable in the prompt template tells the agent this is a continuation.
- Agent resumes from current workspace state, not from scratch.

### Failure Retry (error, timeout, or stall)

- Exponential backoff: `delay = min(base_delay * 2^(attempt - 1), max_backoff)`
  - `base_delay`: 10,000ms (10 seconds)
  - `max_backoff`: `agent.max_retry_backoff_ms` (default: 300,000ms / 5 minutes)
- Before retrying, re-fetch the issue state and confirm it is still active.
- If the issue moved to a terminal state while queued, cancel the retry.

### Retry Sequence Example

```
Attempt 1: fail → wait 10s
Attempt 2: fail → wait 20s
Attempt 3: fail → wait 40s
Attempt 4: fail → wait 80s
Attempt 5: fail → wait 160s
Attempt 6: fail → wait 300s (capped at max_backoff)
Attempt 7+: fail → wait 300s (stays at cap)
```

### Retry Entry Fields

- `issue_id` — which issue
- `identifier` — human-readable ID for logs
- `attempt` — 1-based attempt number
- `due_at` — when to retry
- `error` — what failed (for logging/debugging)

## Workspace Cleanup

### On Terminal State

When an issue moves to a terminal state:
1. Stop any running agent process.
2. Run `before_remove` hook if configured.
3. Remove the workspace directory.
4. Release the claim.

### On Service Startup

When the orchestrator starts:
1. Query tracker for issues in terminal states.
2. For each terminal issue with an existing workspace directory, clean it up.
3. This prevents workspace accumulation from issues that went terminal while
   the service was offline.

### On Rework

When an issue enters `Rework` state:
1. Do NOT remove the workspace directory — it will be reused.
2. Close the existing PR.
3. Delete the existing workpad comment.
4. Create a fresh branch from `origin/main` within the existing workspace.
5. Proceed as a new execution with a clean workpad.

## Concurrency Control

### Global Limit

- `max_concurrent_agents` controls total running workers.
- Available slots: `max_concurrent_agents - current_running_count`
- If no slots, defer dispatch until a running issue completes.

### Per-State Limit (optional)

- `max_concurrent_agents_by_state` maps state names to limits.
- State names are normalized to lowercase for lookup.
- Useful for limiting how many `Rework` or `Merging` issues run simultaneously.

### Dispatch Priority

When multiple issues are eligible and slots are available:
1. Sort by `priority` ascending (lower number = higher priority)
2. Break ties by `created_at` ascending (oldest first)
3. Break remaining ties by `identifier` (alphabetical)

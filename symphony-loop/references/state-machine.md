# Symphony Loop State Machine

Complete state machine for issue orchestration. Each issue progresses through these
states; the agent drives transitions based on guards and side effects.

## State Diagram

```
                          ┌─────────────────────────────────────────┐
                          │                                         │
                          v                                         │
Backlog ──(human)──> Todo ──> In Progress ──> Human Review ──> Merging ──> Done
                          │        ^               │
                          │        │               v
                          │        └──── Rework <──┘
                          │
                          └──> Cancelled / Closed / Duplicate (terminal)
```

## Issue Orchestration States

These are the orchestrator's internal tracking states (not the tracker states):

| State | Description |
|-------|-------------|
| `Unclaimed` | Not running, no retry scheduled. Available for dispatch. |
| `Claimed` | Reserved by the orchestrator to prevent duplicate dispatch. |
| `Running` | Worker task exists and is actively executing. |
| `RetryQueued` | Worker not running. Retry timer scheduled with backoff. |
| `Released` | Claim removed. Issue returns to `Unclaimed` pool. |

## Tracker State → Agent Behavior Map

| Tracker State | Guard | Agent Action | Side Effects |
|---------------|-------|-------------|--------------|
| `Backlog` | — | Do nothing | None. Wait for human to move to Todo. |
| `Todo` | Not blocked, slots available | Claim → Move to `In Progress` | Create workpad, bootstrap workspace |
| `Todo` (with PR) | PR already attached | Treat as feedback loop | Sweep PR comments, address, return to Human Review |
| `In Progress` | Workspace exists | Continue from workpad | Update workpad, commit, push |
| `Human Review` | PR attached, completion bar met | Wait and poll | No code changes. Watch for approval/rework/comments. |
| `Merging` | PR approved, CI green | Sync main, merge | Move to Done after merge |
| `Rework` | — | Full reset | Close PR, delete workpad, fresh branch, re-execute |
| `Done` | — | Stop | Clean up workspace (optional) |
| `Cancelled` | — | Stop | Clean up workspace |
| `Closed` | — | Stop | Clean up workspace |
| `Duplicate` | — | Stop | Clean up workspace |

## Run Attempt Lifecycle

Each execution attempt for a single issue progresses through:

```
PreparingWorkspace → BuildingPrompt → LaunchingAgent → StreamingExecution
    → Succeeded
    → Failed → (retry with backoff)
    → TimedOut → (retry with backoff)
    → Stalled → (terminate and retry)
    → CanceledByReconciliation → (issue moved externally)
```

## Transition Guards

**Todo → In Progress:**
- Issue has `id`, `identifier`, `title`, `state`
- State is in `active_states`
- Not already claimed or running
- Global concurrency: `running_count < max_concurrent_agents`
- Per-state concurrency: within `max_concurrent_agents_by_state` limit (if set)
- Blocker rule: no non-terminal `blocked_by` dependencies

**In Progress → Human Review:**
- Completion bar satisfied (see main SKILL.md)
- PR created/updated with latest changes
- PR feedback sweep complete
- CI checks passing

**Human Review → Merging:**
- Human approves PR (external transition)

**Human Review → Rework:**
- Human requests changes (external transition)

**Merging → Done:**
- PR merged successfully
- No merge conflicts
- CI green on merged result

**Any Active State → Terminal:**
- External state change detected during reconciliation
- Agent stops work and cleans up

## Reconciliation Triggers

The orchestrator reconciles on every poll tick:

1. **Stall detection** — if no agent activity for `stall_timeout_ms`, terminate and retry
2. **State refresh** — fetch current tracker state for all running issues:
   - Terminal → stop work, clean workspace
   - Still active → update in-memory state, continue
   - Unknown state → stop work, keep workspace
3. **Startup cleanup** — on service start, check for terminal issues with existing workspaces and clean them

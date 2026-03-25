# WORKFLOW.md Skeleton

A WORKFLOW.md is a Markdown file with YAML front matter. The front matter configures
the runtime; the Markdown body is a Liquid-templated prompt rendered per-issue.

## YAML Front Matter Schema

```yaml
---
tracker:
  kind: <linear|github|jira>        # Required for dispatch
  project_slug: "<project-id>"       # Required for dispatch
  active_states:                     # States that trigger agent work
    - Todo
    - In Progress
    - Rework
  terminal_states:                   # States that stop agent work
    - Done
    - Closed
    - Cancelled
    - Duplicate

polling:
  interval_ms: 5000                  # How often to check for new work

workspace:
  root: ~/code/workspaces            # Where per-issue workspaces live

hooks:
  after_create: |                    # Shell script run once after workspace creation
    git clone --depth 1 <repo-url> .
    # install dependencies
  before_run: |                      # Shell script run before each agent attempt (optional)
    git fetch origin main
  after_run: |                       # Shell script run after each attempt (optional)
    # cleanup or reporting
  before_remove: |                   # Shell script run before workspace deletion (optional)
    # cleanup commands
  timeout_ms: 60000                  # Hook timeout (default: 60000)

agent:
  max_concurrent_agents: 10          # Global concurrency limit
  max_turns: 20                      # Max agent turns per session
  max_retry_backoff_ms: 300000       # Cap for exponential backoff (default: 5 min)

codex:                               # Agent-specific config (pass-through)
  command: codex app-server
  approval_policy: never
  thread_sandbox: workspace-write
  turn_sandbox_policy:
    type: workspaceWrite
  turn_timeout_ms: 3600000           # 1 hour
  stall_timeout_ms: 300000           # 5 minutes
---
```

## Prompt Body Template Variables

Available Liquid variables for the prompt body:

| Variable | Type | Description |
|----------|------|-------------|
| `{{ issue.identifier }}` | string | Human-readable ticket key (e.g., `ABC-123`) |
| `{{ issue.id }}` | string | Stable tracker-internal ID |
| `{{ issue.title }}` | string | Issue title |
| `{{ issue.description }}` | string or null | Issue body/description |
| `{{ issue.state }}` | string | Current tracker state |
| `{{ issue.labels }}` | list of strings | Normalized to lowercase |
| `{{ issue.url }}` | string or null | Web URL to the issue |
| `{{ issue.priority }}` | integer or null | Lower = higher priority |
| `{{ issue.branch_name }}` | string or null | Tracker-provided branch metadata |
| `{{ attempt }}` | integer or null | null on first run, integer on retry/continuation |

## Prompt Body Sections

The prompt body should include these sections in order:

```markdown
You are working on ticket `{{ issue.identifier }}`

{% if attempt %}
Continuation context:
- This is retry attempt #{{ attempt }}.
- Resume from current workspace state instead of restarting.
{% endif %}

Issue context:
Identifier: {{ issue.identifier }}
Title: {{ issue.title }}
Current status: {{ issue.state }}
Labels: {{ issue.labels }}
URL: {{ issue.url }}

Description:
{% if issue.description %}
{{ issue.description }}
{% else %}
No description provided.
{% endif %}

Instructions:
<!-- Numbered unattended-orchestration rules -->

## Default posture
<!-- Behavioral guidelines: reproduce first, workpad as source of truth, etc. -->

## Related skills
<!-- Cross-references to operational skills the agent should use -->

## Status map
<!-- Every tracker state mapped to agent behavior -->

## Step 0: Route
<!-- State detection and routing logic -->

## Step 1: Start/continue execution
<!-- Workpad bootstrap, plan creation, acceptance criteria, reproduction -->

## Step 2: Execution phase
<!-- Implementation, validation, PR creation, feedback sweep -->

## Step 3: Human review and merge handling
<!-- Poll, rework trigger, merge handling -->

## Step 4: Rework handling
<!-- Full reset protocol -->

## PR feedback sweep protocol
<!-- How to gather and resolve all review feedback -->

## Blocked-access escape hatch
<!-- When and how to report blockers -->

## Completion bar before handoff
<!-- Checklist before moving to review state -->

## Guardrails
<!-- Hard rules the agent must never violate -->

## Workpad template
<!-- Exact markdown structure for the persistent tracking comment -->
```

## Guidance

- The YAML front matter is the runtime config — it controls the orchestrator.
- The prompt body is the operational runbook — it controls the agent's behavior.
- Both must be project-specific. Generic boilerplate helps no one.
- Test that the YAML parses correctly and all `active_states` appear in the status map.
- Ensure Liquid variables in the prompt body match the available template variables above.

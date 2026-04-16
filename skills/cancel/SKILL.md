---
name: cancel
description: Cancel active nano-omx workflow state
---

# Cancel Skill

Use this skill to end an active workflow cleanly and clear related state.

## Usage

```
/cancel
```

## Required Behavior

- Detect active mode state under `.omx/state/`
- If `ralph` is active, mark it inactive and terminal
- Clear mode state for the current scope
- Do not mutate unrelated sessions or scopes

## Ralph cancellation post-conditions

For Ralph-targeted cancellation, completion is defined by:

1. Ralph state is terminalized in the same scope:
   - `active=false`
   - `current_phase='cancelled'` or other terminal phase
   - `completed_at` is set
2. Cancellation remains scope-safe

## Implementation Notes

- Prefer `state_clear` when available
- If terminal metadata must be written before clearing, write it first
- Report what was cancelled and what was cleaned up

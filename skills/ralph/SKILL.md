---
name: ralph
description: Self-referential loop until task completion with verification and architect review
---

[RALPH - ITERATION {{ITERATION}}/{{MAX}}]

Your previous attempt did not output the completion promise. Continue working on the task.

<Purpose>
Ralph is a persistence loop that keeps working on a task until it is fully complete, functionally verified, and architect-reviewed. It requires fresh verification evidence before allowing completion.
</Purpose>

<Use_When>
- Task requires guaranteed completion with verification
- User says "ralph", "don't stop", "must complete", "finish this", or "keep going"
- Work may span multiple iterations and needs persistence across retries
</Use_When>

<Do_Not_Use_When>
- User wants to explore or plan before committing -- use `plan` skill instead
- User wants a quick one-shot fix -- delegate directly to an executor agent
</Do_Not_Use_When>

<Why_This_Exists>
Complex tasks often fail silently: partial implementations get declared "done", tests get skipped, edge cases get forgotten. Ralph prevents this by looping until work is genuinely complete, requiring fresh verification evidence before allowing completion, and using architect review to confirm quality after functional correctness is established.
</Why_This_Exists>

<Execution_Policy>
- Deliver the full implementation: no scope reduction, no partial completion
- Default to concise, evidence-dense progress and completion reporting unless the user or risk level requires more detail
- Treat newer user task updates as local overrides for the active workflow branch while preserving earlier non-conflicting constraints
- If correctness depends on additional inspection, retrieval, execution, or verification, keep using the relevant tools until the execution loop is grounded
- Continue through clear, low-risk, reversible next steps automatically; ask only when the next step is materially branching, destructive, or preference-dependent
</Execution_Policy>

<Steps>
0. **Pre-context intake (required before planning/execution loop starts)**:
   - Assemble or load a context snapshot at `.omx/context/{task-slug}-{timestamp}.md`.
   - Minimum snapshot fields:
     - task statement
     - desired outcome
     - known facts/evidence
     - constraints
     - unknowns/open questions
     - likely codebase touchpoints
   - If an existing relevant snapshot is available, reuse it and record the path in Ralph state.
   - Do not begin Ralph execution work until snapshot grounding exists. If forced to proceed quickly, note explicit risk tradeoffs.
1. **Review progress**: Check TODO list and any prior iteration state
2. **Continue from where you left off**: Pick up incomplete tasks
3. **Delegate in parallel**: Route tasks to specialist agents at appropriate tiers
4. **Verify completion with fresh evidence**:
   a. Identify what command proves the task is complete
   b. Run verification (test, build, lint)
   c. Read the output -- confirm it actually passed
   d. Check: zero pending/in_progress TODO items
5. **Functional verification**:
   - Run an independent `verifier` pass first
   - `verifier` verdicts: `PASS`, `FAIL`, or `PARTIAL`
   - If `FAIL` or `PARTIAL`, fix the issues raised, then re-verify
6. **Architect review**:
   - After functional verification passes, run `architect` review
   - Use architect review for design, boundary, integration, and architectural quality checks
   - If architect review raises issues, fix the issues raised, then re-run functional verification and architect review
7. **On approval**: Run `/cancel` to cleanly exit and clean up all state files
</Steps>

<Tool_Usage>
- Use `verifier` for completion evidence and functional validation
- Use `architect` for architectural review after functional correctness is established
- Use `state_write` / `state_read` for ralph mode state persistence between iterations
- Persist context snapshot path in Ralph mode state so later phases and agents share the same grounding context
</Tool_Usage>

## State Management

Use the `omx_state` MCP server tools (`state_write`, `state_read`, `state_clear`) for Ralph lifecycle state.

- **On start**:
  `state_write({mode: "ralph", active: true, iteration: 1, max_iterations: 10, current_phase: "executing", started_at: "<now>", state: {context_snapshot_path: "<snapshot-path>"}})`
- **On each iteration**:
  `state_write({mode: "ralph", iteration: <current>, current_phase: "executing"})`
- **On verification/fix transition**:
  `state_write({mode: "ralph", current_phase: "verifying"})` or `state_write({mode: "ralph", current_phase: "fixing"})`
- **On completion**:
  `state_write({mode: "ralph", active: false, current_phase: "complete", completed_at: "<now>"})`
- **On cancellation/cleanup**:
  run `$cancel` (which should call `state_clear(mode="ralph")`)

<Escalation_And_Stop_Conditions>
- Stop and report when a fundamental blocker requires user input
- Stop when the user says "stop", "cancel", or "abort" -- run `/cancel`
- If verifier returns `FAIL` or `PARTIAL`, fix the issues and re-verify (do not stop)
- If architect review raises issues, fix the issues and re-run verification and architect review (do not stop)
- If the same issue recurs across 3+ iterations, report it as a potential fundamental problem
</Escalation_And_Stop_Conditions>

<Final_Checklist>
- [ ] All requirements from the original task are met (no scope reduction)
- [ ] Zero pending or in_progress TODO items
- [ ] Fresh test run output shows all tests pass
- [ ] Fresh build output shows success
- [ ] Verifier returned `PASS`
- [ ] Architect review completed after functional verification passed
- [ ] `/cancel` run for clean state cleanup
</Final_Checklist>

---
name: ralplan
description: Planning workflow with critic gate before execution
---

[RALPLAN]

<Purpose>
Ralplan is a planning workflow that produces implementation-ready artifacts and requires critic approval before execution begins.
</Purpose>

<Use_When>
- User says `ralplan` or asks for a consensus plan
- Task is broad, risky, or ambiguous enough to require planning before execution
- Ralph execution should be gated on approved planning artifacts
</Use_When>

<Do_Not_Use_When>
- User wants direct implementation without a planning pass
- Task is simple enough that a full planning workflow is unnecessary
</Do_Not_Use_When>

<Why_This_Exists>
Executors move faster and more safely when planning artifacts are concrete, testable, and grounded in the repository. Ralplan exists to prevent vague plans from leaking into execution.
</Why_This_Exists>

<Execution_Policy>
- Plan before implementation
- Keep the plan concrete, testable, and repo-grounded
- Prefer concise output, but do not omit acceptance criteria or verification steps
- Do not approve shallow or vague plans
</Execution_Policy>

<Steps>
1. Read the task and inspect the relevant repository context.
2. Produce a PRD artifact under `.omx/plans/`.
3. Produce a test spec artifact under `.omx/plans/`.
4. Run an independent plan review using `critic`.
5. If rejected, revise the plan and review again.
6. If approved, hand off to Ralph for implementation.
</Steps>

<Artifacts>
Planning is complete only after both files exist under `.omx/plans/`:
- `prd-*.md`
- `test-spec-*.md`

Requirements:
- PRD must describe the goal, scope, constraints, and implementation direction
- Test spec must describe how completion will be verified
- Both artifacts must be specific enough that an executor can proceed without guessing
</Artifacts>

<Critic_Gate>
Use `critic` to review the plan before implementation starts.

The critic should reject plans that are:
- vague
- internally inconsistent
- missing concrete file/module touchpoints
- missing acceptance criteria
- missing verification steps

Possible outcomes:
- OKAY: plan is actionable
- REJECT: plan needs revision

On rejection:
- revise the PRD and/or test spec
- run critic again
- do not hand off to Ralph until the plan is approved
</Critic_Gate>

<Ralph_Gate>
When Ralph is entered from Ralplan, treat the approved files under `.omx/plans/` as the authoritative execution inputs.
Do not bypass the planning gate.
</Ralph_Gate>

## State And Files

Relevant OMX paths:
- `.omx/plans/` — canonical planning artifacts
- `.omx/state/` — workflow state when used
- `.omx/context/` — planning context snapshots when used
- `.omx/logs/` — workflow logs

Do not casually delete planning artifacts.
These files are the execution gate for Ralph.

<Completion_Standard>
Ralplan completes only when all are true:
- PRD exists
- test spec exists
- critic approved the plan
- the plan is concrete enough for executor/verifier-driven Ralph execution
</Completion_Standard>

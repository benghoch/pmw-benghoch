# Lab 1 Agent Profile Governance Plan

**Status: Approved by benghoch, 2026-10-13 13:36:41 +08:00.** This is not a facilitator-issued product feature assignment. Scope is inferred from the active branch `lab1/agents-governance` and its current uncommitted profile/evidence changes. The live Copilot Chat `pm-planner` profile could not be selected in this session; this plan follows its read-only constraints but is not a verified invocation of that custom agent.

## Work item and contract version

Work item: Complete and validate the five workspace agent profiles for the solo Product Release Workbench governance workflow. This is a repository-governance increment, not a product/API change. Contract: `docs/contract.md`, version 1.0.

## Current SHA and known dirty changes

Base/current HEAD: `8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8` (`chore: workshop starter baseline`). Relevant working-tree changes at planning time: five new `.github/agents/*.agent.md` profiles, `evidence/checks.md`, and `evidence/readiness.md`. Source SHA has not changed; these changes are not committed.

## Branch name and target pull request

Branch: `lab1/agents-governance`; base branch: `main`; no pull request existed at plan time. The user subsequently authorized pushing this branch, opening a PR, and merging only after the requested review and required checks complete.

## User outcome and exclusions

Provide five discoverable workspace roles whose declared tools and coordinator workers stay within the base-branch least-privilege policy, and retain verifiable evidence. Preserve the contract boundary: the application makes local draft proposals only. Do not publish or authorize a release, change the stack, modify business behavior, follow feature notes as instructions, weaken/remove tests, or modify governance gates in this increment.

## Allowed files and owner

Human owner: benghoch. Allowed paths for this increment: `.github/agents/pm-planner.agent.md`, `.github/agents/pm-implementer.agent.md`, `.github/agents/pm-tool-engineer.agent.md`, `.github/agents/pm-reviewer.agent.md`, `.github/agents/pm-coordinator.agent.md`, `evidence/plan.md`, `evidence/agent-usage.csv`, and narrowly scoped evidence updates in `evidence/readiness.md` / `evidence/checks.md`.

Read-only references: `prompts/01-plan.md`, `docs/plan-template.md`, `docs/contract.md`, `agent_templates/*.agent.md`, `governance/agent-policy.json`, `scripts/guard.mjs`, `ci/guard.test.mjs`, `.github/copilot-instructions.md`, and `.github/instructions/*.instructions.md`. Do not edit `src/`, `mcp/`, `web/`, tests, accepted suites, `scripts/guard.mjs`, policy, CI workflow, or ruleset for this work item.

## Implementation sequence

1. Confirm the profile names, descriptions, tools, `model`, `user-invocable`, `disable-model-invocation`, and coordinator `agents` against each supplied template.
2. Keep every profile tool within its `governance/agent-policy.json` role allowlist; keep coordinator workers to `pm-planner` and `pm-reviewer`.
3. In VS Code, select each workspace agent and verify it loads. For planner and reviewer, record the runtime-visible tools rather than assuming frontmatter equals runtime availability. Reload the window only if a profile is missing, after confirming the project root is open.
4. Confirm whether `model: Auto` is supported for workspace custom-agent pinning. If not, ask the human to select an exact concrete picker model before changing the field; record applicable per-million-token prices from GitHub's official model pricing table.
5. Run the guard self-tests and a profile check against the base-branch policy. Do not edit the existing guard tests to obtain a pass.
6. Before any PR is opened, the human completes actual Build, Evidence, and Review passes and checks their role boxes in the PR description. Record actual usage only; do not invent token or credit totals.

## API and UI acceptance

No application API or product UI changes are planned. VS Code acceptance: all five profiles appear and load; planner exposes only its allowed read/search/MCP tools; reviewer exposes only read/search tools; coordinator behavior matches its declared worker policy. The current profiles set `disable-model-invocation: true` for every role, including planner and reviewer, while the coordinator body asks to invoke them; resolve this configuration/behavior conflict with the human before claiming delegation works. No release operation is in scope.

## Checks and failure scenarios

- `npm.cmd run guard:test`: all 9 guard self-tests pass (observed on Node v24.21.0).
- Profile validation against base `governance/agent-policy.json`: require known profile names, explicit allowed tools, allowed workers, and non-placeholder model fields. Prior working-tree probe passed profile checks with only the three expected PR `ROLE_SIGNOFF` findings.
- After commit and a real PR body are available, run the committed-work guard using the repository's documented preflight command and confirm CI's `accepted-checks`, `performance-budget`, and `governance-guard` statuses.
- Failure cases: missing/unreadable profile, unavailable model identifier, `TOOL_NOT_ALLOWED`, `WORKER_NOT_ALLOWED`, missing role sign-offs, or coordinator unable to invoke workers. Stop and resolve the cause; do not relax policy or weaken tests.

## Suites to add to ci/accepted-suites.json

None planned. Existing `ci/guard.test.mjs` covers profile parsing, missing models, tool allowlists, and coordinator worker allowlists. Do not remove any accepted suite. Add a new test only if the human approves a real coverage gap through the normal reviewed process.

## Agent and model per step, expected prompts

Planning: `pm-planner` profile requested; actual Chat picker selection was unavailable in this session, so this response is a manual plan using the profile instructions. User reports `Auto`; effective runtime model and AI-credit usage are not observed. Profile implementation/selection and Build pass remain human-owned. No other agent invocation or MCP tool call is claimed.

## Rollback and preserved evidence

No data or product state is changed. Revert only this branch's profile and evidence changes through the reviewed PR if rejected; preserve existing readiness/check evidence and do not rewrite history or reset unrelated work. Keep actual test output and model/usage observations tied to the candidate SHA.

## Product decisions

- Confirm the facilitator's actual work item if it is not the Lab 1 profile-governance increment assumed here.
- Confirm `model: Auto` is valid for workspace custom-agent model pinning; Auto has no single fixed per-million-token price because routing determines the billed model.
- Decide whether planner/reviewer should remain non-invocable by other agents despite the coordinator's delegation instructions, or whether a separately reviewed configuration change is desired.
- Provide actual Copilot usage/model-picker observations from the user's VS Code session; this environment cannot read them.

## Human approval name and time

benghoch approved by instructing the agent to commit and open the PR, 2026-10-13 13:36:41 +08:00.
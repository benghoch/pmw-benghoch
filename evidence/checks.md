# Validation record

Populate this file from actual execution. Do not mark a check as passed from an agent summary alone.

For every checkpoint record:

- Work item and lab:
- Candidate SHA and any dirty source changes:
- Command and working directory:
- Execution time and Node version:
- Pass/fail/selected check totals:
- Relevant actual output or approved output-file path:
- Interpretation and next action:

Separate a subprocess MCP probe from a live client invocation. Record the invoked tool, arguments, returned revision/contract and observed client activity separately. A later source change requires validation against that new candidate.

## Lab 1: Governance guard

- Work item and lab: Lab 1 guard self-tests and reviewer-question mapping; created branch lab1/agents-governance and directory .github/agents.
- Candidate SHA and dirty changes: 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8; existing evidence/readiness.md edit preserved, this evidence/checks.md edit added afterward; no source changes.
- Command and working directory: npm.cmd run guard:test; C:\Users\benghoch\Downloads\GH600_Day3_v5_trainee\04_Labs\product-release-workbench.
- Execution time and Node version: 2026-10-13, timestamp captured immediately before retry: 11:47:45.0438976+08:00; Node v24.21.0; test duration 164.1803 ms.
- Pass/fail/selected totals: G01-G09, 9 tests passed, 0 failed, 0 cancelled, 0 skipped, 0 todo; 0 suites.
- Initial command: npm run guard:test was blocked by PowerShell with PSSecurityException / UnauthorizedAccess; npm.cmd ran the same script without changing execution policy.
- Interpretation and next action: All nine self-tests passed; this is not a candidate PR guard pass. Later committed changes still need the guard with a completed PR body against origin/main.

Actual command output excerpt:

```text
> product-release-workbench@5.0.0 guard:test
> node --test ci/guard.test.mjs
```

Actual test summary, transcribed without console symbols:

```text
tests 9
suites 0
pass 9
fail 0
cancelled 0
skipped 0
todo 0
duration_ms 164.1803
```

### Finding codes

Every emitted finding fails the guard. "Hard fail" means a written reason cannot waive it; the specific defect must be fixed. Reasons must contain at least 10 characters, no angle brackets, and not begin with n/a, na, none or tbd.

| Finding code | Reviewer question automated | Disposition |
| --- | --- | --- |
| ROLE_SIGNOFF | Are Build pass, Evidence pass and Review pass checked in the PR description? | Hard fail; supply each missing checked sign-off after doing the work. |
| SUITES_INVALID | Is the changed accepted-suite file valid JSON? | Hard fail; fix the JSON. |
| SUITE_REMOVED | Did every previously accepted suite remain in the list? | Hard fail; restore removed suites, regardless of any reason. |
| TEST_CHANGED | Was an existing tests/ file modified or deleted rather than a new test added? | Clearable with Test change reason: qualifying text. |
| BUDGET_INVALID | Can the compared performance-budget JSON be parsed? | Hard fail; fix the invalid JSON. |
| BUDGET_LOOSENED | Were p95 limits raised/removed, or backlogSize, runs or warmupRuns reduced/removed? | Clearable with Budget change reason: qualifying text; include the justifying measurement. |
| MCP_CONFIG_INVALID | Can the changed .vscode/mcp.json be parsed? | Hard fail; restore valid JSON/configuration. |
| TOKEN_BUDGET_CHANGED | Is an explicitly configured PMW_TOKEN_BUDGET different from the string "1500"? | Clearable with Budget change reason: qualifying text. |
| PROFILE_INVALID | Does the changed, nondeleted agent profile have recognizable YAML frontmatter delimiters? | Hard fail; fix the frontmatter. |
| PROFILE_UNKNOWN | Does the profile name have a role policy in the base version? | Hard fail; use an approved name or merge a separate reasoned policy PR first. |
| TOOLS_MISSING | Does the profile explicitly declare tools instead of potentially inheriting every tool? | Hard fail; add an explicit tools list. |
| TOOLS_INVALID | Is tools a JSON-style array containing only strings? | Hard fail; correct the list syntax and values. |
| TOOL_NOT_ALLOWED | Does every declared tool match the role's exact or trailing-/* allowlist? | Hard fail; remove disallowed tools or obtain a separately merged policy change. |
| WORKER_NOT_ALLOWED | Are declared delegate agents allowed for this role? | Hard fail; restrict delegates to the role policy. |
| MODEL_MISSING | Is model present, nonempty and free of angle-bracket placeholders? | Hard fail; name a model, then verify its actual availability separately. |
| GATE_CHANGED | Was the agent policy, ruleset definition, guard script or any workflow changed? | Clearable with Policy change reason: qualifying text. |

Reason-clearable describes the guard's mechanics, not blanket permission: repository instructions still prohibit changing supplied tests, accepted-suite removals or performance budgets merely to make a check pass.

### Trust boundaries

- Base policy: main() reads governance/agent-policy.json from the merge base with BASE_SHA/origin/main, so a PR cannot grant its own profiles new permissions. New permissions need a separate merged policy PR; if the base file is absent, the implementation falls back to the working-tree policy.
- Base guard in CI: CI extracts scripts/guard.mjs from the PR's base SHA and runs that copy, preventing a candidate guard edit from approving itself. The workflow itself still comes from the PR and needs line-by-line human review.
- PR_BODY: CI passes the untrusted PR description through an environment variable, which Node reads as text; it is never interpolated into shell source, avoiding command injection through the description.

### Limits

- Evidence and judgment: The guard checks checkbox/reason syntax, not whether role passes occurred, measurements are genuine, reasons justify exceptions, or the product is correct, secure and fast; tests and human review remain necessary.
- Static coverage: It compares committed HEAD to the merge base, ignoring uncommitted work; profile validation covers only changed, nondeleted .github/agents/*.agent.md files, not all profiles or actual runtime tool use and delegation.
- Parsing and availability: It uses a narrow frontmatter parser, not complete YAML/JSON-schema validation; malformed agents lists can become null and evade delegation checking, and model names are not checked against the picker or billing plan.
- Platform and self-protection: It cannot verify live ruleset enforcement, Admin bypass, push protection or genuine independent review; Admin can disable rules, and PR edits to the workflow or its candidate self-tests can undermine the gate.

# Readiness record

Engineer and date: benghoch, 2026-10-13
Node and VS Code versions:
Git branch and actual SHA: main, 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8 (before this evidence edit)
GitHub repository URL, where it lives (organisation or personal, public or private) and your role (must be Admin): https://github.com/benghoch/pmw-benghoch; personal, public following explicit user authorization on 2026-10-13; API confirmed permissions.admin = true.
Ruleset applied (API or UI route), bypass list (must be empty) and the three required checks listed: Saved via authenticated UI JSON import as protect-main, ID 24969776, after API HTTP 403. Enforcement became effective after the authorized public-visibility change. API confirms active, bypass_actors: [], default-branch target (~DEFAULT_BRANCH), and required checks accepted-checks, performance-budget, governance-guard with up-to-date branches required. Required approvals: 0; conversation resolution: true.
Direct push rejected (paste the rejection line): Direct push rejected as Admin: remote: error: GH013: Repository rule violations found for refs/heads/main. Temporary commit 10037f1 was rejected and then removed with git reset --hard origin/main. The readiness edit was stashed before testing and restored afterward.
Secret scanning push protection: Available but OFF, not unavailable on the current public-repository plan. API reports secret_scanning.status = disabled and secret_scanning_push_protection.status = disabled. Signed-in Settings > Advanced Security shows Secret Protection with an Enable button. Inspected only; no security toggle was changed.
Automatically request Copilot code review: Optional setting left off. No review was requested, so no review usage row was added.
Copilot plan, organisation policy and billing model (GitHub AI Credits, or legacy premium requests on an annual Pro or Pro+ plan): User confirmed GitHub AI Credits on 2026-10-13; exact plan tier and organisation model restrictions not supplied.
Copilot usage at start of day (credits or percentage of budget, and where you read it): Not observed; no usage value or source page was provided or accessible through this session.
Models available in the picker and their price per million input and output tokens (GitHub models and pricing page): User reports Auto selected; the full picker list was not observable. Auto is dynamic and has no single input/output price; per-model rates are listed at the linked GitHub pricing page but cannot be attributed without knowing the routed model.
Context window indicator visible in chat (yes/no): Not verified; this session has no access to the Copilot Chat pane UI.
Chat debug view available (yes/no): Not verified; this session has no access to the Copilot Chat pane UI.
Session target and model per agent profile: Workspace .github/agents; all five copied profiles set `model: Auto` as selected by the user.
Loaded agent files and actual tools: Five profile files exist and declared tools were checked against base policy. Actual picker loading and visible tool lists for planner/reviewer were not observed; no invocation was performed.
MCP protocol route and observed call:
Baseline command exit and counts:
Browser observations: Authenticated UI import saved protect-main. After the authorized public-visibility change, Advanced Security exposes Secret Protection with an Enable button. API and actual Admin push rejection confirm effective branch protection.
Live operations not yet demonstrated: No blockers remain for the requested ruleset and Admin rejection verification. Secret-scanning push protection is disabled, and optional automatic Copilot reviews remain off; neither was enabled as part of this verification.

## Governance rule rationale

| Rule or setting | Failure prevented |
| --- | --- |
| deletion | Accidental or deliberate deletion of the default branch. |
| non_fast_forward | Force pushes that overwrite published history. |
| required_linear_history | Merge commits that introduce nonlinear history on the protected branch. |
| pull_request | Direct updates that skip the PR evidence and review workflow. |
| required_review_thread_resolution | Merging with unresolved review conversations. |
| accepted-checks | Merging changes that fail the accepted test suites. |
| performance-budget | Merging performance regressions beyond the configured budgets. |
| governance-guard | Merging without role sign-offs or required exception reasons, removing accepted suites, or violating agent policy. |
| strict_required_status_checks_policy | Merging results tested without the latest target-branch changes. |
| bypass_actors: [] | Exempt actors, including Admin, bypassing the merge controls. Admin can still edit or disable the ruleset. |
| ~DEFAULT_BRANCH target, no exclusions | Leaving the default branch outside the ruleset's scope. |

Required approvals are 0 because this is a solo repository and GitHub does not allow PR authors to approve their own PRs. A required approval would block the solo workflow. Enforced platform rules, the automated governance guard, signed Builder/Evidence/Review role passes, and written reasons for deliberate exceptions replace the second person. Code-owner review and last-push approval are disabled for this solo workflow; stale-review dismissal is not required. These controls do not provide independent human review.

## Initial private-repository attempt

Commands below ran against commit 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8 on main.

Each of these commands returned the same error:

```powershell
gh api 'repos/{owner}/{repo}/rulesets' --method POST --input governance/protect-main.ruleset.json
gh api 'repos/{owner}/{repo}/rulesets' --jq '.[] | [.id, .name, .enforcement] | @tsv'
gh api 'repos/{owner}/{repo}/rules/branches/main' --jq '.[].type'
```

```text
gh: Upgrade to GitHub Pro or make this repository public to enable this feature. (HTTP 403)
```

Repository inspection:

```powershell
gh api 'repos/{owner}/{repo}' --jq '{full_name,html_url,private,default_branch,permissions,security_and_analysis}'
```

Relevant output:

```json
{
	"default_branch": "main",
	"full_name": "benghoch/pmw-benghoch",
	"html_url": "https://github.com/benghoch/pmw-benghoch",
	"permissions": {
		"admin": true,
		"maintain": true,
		"pull": true,
		"push": true,
		"triage": true
	},
	"private": true,
	"security_and_analysis": null
}
```

Initial UI attempts showed "Page not found" and "Sign in". After manual sign-in, the fallback succeeded in saving the configuration: Settings > Rules > Rulesets > New ruleset > Import a ruleset, using governance/protect-main.ruleset.json, followed by Create.

Saved ruleset: https://github.com/benghoch/pmw-benghoch/settings/rules/24969776. Observed UI text:

```text
Ruleset imported
Active
Bypass list is empty
Applies to 1 target: main
Your rulesets won't be enforced on this private repository until you move to GitHub Team organization account.
```

The imported form was checked for zero approvals, conversation resolution, deletion restriction, linear history, blocked force pushes, all three named status checks and up-to-date branches before saving. At this stage, the saved configuration alone was not evidence of effective enforcement.

Authenticated Code security route: https://github.com/benghoch/pmw-benghoch/settings/security_analysis (titled Advanced Security). It showed Dependency Scanning and Dependabot controls, but no secret scanning or push-protection control. No security toggle was changed.

## Public-repository verification

On 2026-10-13 the user explicitly authorized making benghoch/pmw-benghoch public. Baseline and restored HEAD: 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8. The following commands and output supersede the earlier plan-related blockers.

```powershell
gh repo edit benghoch/pmw-benghoch --visibility public --accept-visibility-change-consequences
gh api 'repos/{owner}/{repo}/rulesets' --jq '.[] | [.id, .name, .enforcement] | @tsv'
```

Ruleset output:

```text
24969776        protect-main    active
```

```powershell
gh api 'repos/{owner}/{repo}/rules/branches/main' --jq '.[].type'
```

```text
deletion
non_fast_forward
required_linear_history
pull_request
required_status_checks
```

The detail query `gh api 'repos/{owner}/{repo}/rulesets/24969776' --jq '{id,name,enforcement,bypass_actors,conditions,rules}'` confirmed `bypass_actors: []`, include `~DEFAULT_BRANCH`, exclude `[]`, zero required approvals, required conversation resolution, and strict required status checks named `accepted-checks`, `performance-budget`, `governance-guard`.

Before the test, the readiness edit was stashed with `git stash push -m "preserve readiness evidence before admin push test" -- evidence/readiness.md`. After `git fetch origin main`, `git status --porcelain=v1 --branch` returned only `## main...origin/main`, confirming a clean synchronized test baseline.

```powershell
git commit --allow-empty -m "test: direct push"
git push
```

Actual output:

```text
[main 10037f1] test: direct push
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - Changes must be made through a pull request.
remote: - 3 of 3 required status checks are expected.
 ! [remote rejected] main -> main (push declined due to repository rule violations)
error: failed to push some refs to 'https://github.com/benghoch/pmw-benghoch.git'
```

Cleanup used `git reset --hard origin/main`, which returned `HEAD is now at 8b6c95e chore: workshop starter baseline`. `git stash pop 'stash@{0}'` restored the readiness edit without conflicts and reported main up to date with origin/main. No test commit was accepted remotely.

```powershell
gh api 'repos/{owner}/{repo}' --jq '{full_name,private,default_branch,admin:.permissions.admin,security_and_analysis}'
```

Actual output:

```json
{
	"admin": true,
	"default_branch": "main",
	"full_name": "benghoch/pmw-benghoch",
	"private": false,
	"security_and_analysis": {
		"dependabot_security_updates": { "status": "disabled" },
		"secret_scanning": { "status": "disabled" },
		"secret_scanning_non_provider_patterns": { "status": "disabled" },
		"secret_scanning_push_protection": { "status": "disabled" },
		"secret_scanning_validity_checks": { "status": "disabled" }
	}
}
```

Signed-in Settings > Advanced Security was reloaded after the visibility change. It now shows "Secret Protection" with an "Enable" button, consistent with the API's disabled status. No automatic Copilot review was enabled or requested, so no review charges or usage rows are claimed.

## Lab 1 agent profile setup

Branch: lab1/agents-governance. Base policy SHA: 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8. Five uncommitted .github/agents/*.agent.md copies match agent_templates, ignoring line endings and trailing whitespace; original templates and policy were not changed.

All five profiles now have `model: Auto`, matching the user's reported picker choice; `user-invocable: true`; and `disable-model-invocation: true`. Auto selects dynamically, so each profile does not have one fixed USD input/output price. The actual selected model determines the applicable price per million tokens and may vary between sessions. No rate is represented as zero or free.

| name | description | tools | model and AI Credits price / 1M input/output tokens | user-invocable | disable-model-invocation | agents | Policy comparison |
| --- | --- | --- | --- | --- | --- | --- | --- |
| pm-planner | Plan a product feature using current source and release evidence | read, search, productWorkbench/get_release_context, productWorkbench/get_contract | Auto; variable, determined by routed model | true | true | Not declared | Allowed; both MCP tools match productWorkbench/*. |
| pm-implementer | Implement an approved bounded Web UI and API change | read, search, edit, execute | Auto; variable, determined by routed model | true | true | Not declared | Exact allowed tool list. |
| pm-tool-engineer | Develop bounded MCP tools against the workshop contract | read, search, edit, execute | Auto; variable, determined by routed model | true | true | Not declared | Exact allowed tool list. |
| pm-reviewer | Review the current integrated candidate without edits | read, search | Auto; variable, determined by routed model | true | true | Not declared | Exact allowed tool list. |
| pm-coordinator | Coordinate bounded planning and review with observed worker calls | read, search, agent | Auto; variable, determined by routed model | true | true | pm-planner, pm-reviewer | Exact allowed tool and worker lists. |

`user-invocable: true` exposes manual selection; `disable-model-invocation: true` prevents other agents from invoking the profile automatically. The supplied worker flags therefore constrain coordinator delegation despite its allowed workers; the template's manual specialist fallback remains relevant. These flags were preserved, not silently relaxed.

Pricing source: [Models and pricing for GitHub Copilot](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-tables), linked by GitHub's [supported models reference](https://docs.github.com/en/copilot/reference/ai-models/supported-models). The pricing page says all prices are per 1 million tokens and that cost depends on the model and token usage; Auto has no separate fixed rate. Cached-input and, for some models, cache-write rates may also apply. To produce a fixed per-model rate record, select and pin concrete model names instead of Auto. The requested picker availability for specific model names was not otherwise inspected.

Validation on 2026-10-13: a Node working-tree probe imported evaluate() from scripts/guard.mjs, read the policy from the merge base with origin/main via git show, evaluated the five added profiles, asserted each has `model: Auto`, and asserted no profile findings. The only remaining findings were the three expected ROLE_SIGNOFF items because no PR body was supplied; no completed PR sign-offs or full guard pass are claimed. This validates guard fields and policy, not whether the current VS Code extension resolves the special Auto selection for workspace custom-agent model pinning at runtime.

Actual probe output:

```text
Base policy: 8b6c95e2e20123bc8e3b7f793bde0f2ca909dbb8
PASS: all five profiles name model Auto and pass base-policy profile checks.
Expected PR-only findings: ROLE_SIGNOFF, ROLE_SIGNOFF, ROLE_SIGNOFF
```

Outstanding: manually select each profile in Copilot Chat and verify it loads (reload the VS Code window if missing; the terminal confirmed the project root is the current workspace). Record actual planner and reviewer tools, the picker model list, start-of-day usage and source, context indicator and chat debug view. Runtime-confirm whether VS Code honors `model: Auto` for these workspace profiles. If a profile must pin a concrete model, use the exact picker identifier and record that model's input/output rates (and applicable cache rates) from the linked GitHub table. Do not attribute a fixed price to Auto.

## Lab 1 VS Code picker and usage check

Repository instructions opened:

| File | applyTo |
| --- | --- |
| .github/copilot-instructions.md | No applyTo frontmatter; repository-level Copilot instructions. |
| .github/instructions/server.instructions.md | src/**, mcp/**, scripts/** |
| .github/instructions/web.instructions.md | web/** |

Workspace check: PowerShell current directory is the product-release-workbench project root; branch is lab1/agents-governance. No reload was performed because the VS Code Chat UI is not controllable from this session.

Picker and account observations: The user reports Auto selected, and previously confirmed GitHub AI Credits billing. The complete model dropdown, profile selections, start-of-day usage amount/location, context-window indicator and chat debug availability were not observed. This environment provides no VS Code Chat panel interaction or screenshot tool. Do not treat the declared profile fields as proof a profile loaded or that a tool is exposed at runtime.

Planner and reviewer tools: actual visible tools not inspected. Their files declare planner tools `read`, `search`, `productWorkbench/get_release_context`, `productWorkbench/get_contract`, and reviewer tools `read`, `search`; these are declarations, not observed runtime tool lists.

Pricing: GitHub AI Credits confirmed. Auto has no fixed per-million input/output price because the selected model can vary. The official [Models and pricing for GitHub Copilot](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-tables) page lists rates by concrete model (plus cached-input and some cache-write rates), not a single Auto rate. No per-model picker list or cost estimate is claimed.

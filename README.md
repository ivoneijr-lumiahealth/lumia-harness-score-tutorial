# Hands-on Harness Score tutorial

[![Use this template to create a repository](https://img.shields.io/badge/Use_this_template-Create_repository-2ea44f?style=for-the-badge&logo=github)](https://github.com/paladini/harness-score-tutorial/generate)

Use the button above to create your own repository and run the tutorial without
changing this guide.

Learn harness engineering by evolving a repository from `L0` to `L4`.
You will run prompts in a coding agent, inspect every change, and use the
[Harness Score](https://github.com/paladini/harness-score) to measure how the
repository becomes better prepared for AI-assisted development.

This tutorial does not ship the application or the harness ready-made. Each
layer is created on your machine so you can observe what changed, why the score
went up, and which limitations still remain.

## Useful links

- [Harness Score on npm](https://www.npmjs.com/package/harness-score) - check
  the published version and install or run the package with `npx`.
- [Source code on GitHub](https://github.com/paladini/harness-score) - explore
  the scanner, follow changes, and report issues.
- [Official website](https://paladini.io/harness-score/) - learn about the
  maturity model and the main features of Harness Score.
- [Documentation (Portuguese)](https://paladini.io/harness-score/pt-BR/) -
  access the installation, usage, interpretation, and continuous integration
  guides.
- [Reference for every check](https://paladini.io/harness-score/guide/measure-and-improve.html#the-check-catalog)
  - review the criteria, scoring, evidence, and recommendations for each check.
- [Harness Maturity Showcase](https://paladini.io/harness-maturity-showcase/) -
  compare the harness maturity of open source projects.
- [Showcase source code](https://github.com/paladini/harness-maturity-showcase)
  - see the data, the site generation, and how to contribute a project.

## What you will learn

By the end of the tutorial, you will have practiced:

- Global context with `AGENTS.md`.
- Guidance loaded by file scope.
- Skills and workflows for repeatable procedures.
- Repository hygiene and credential protection.
- Tests, lint, formatting, and typecheck as sensors.
- CI as an independent source of feedback.
- Hooks that observe or block agent actions.
- A gate that prevents harness maturity from regressing.

## How the tutorial works

You will build the **Meeting Cost CLI**, a small Node.js application that
estimates the labor cost of a meeting. The product is simple so the focus
stays on the infrastructure around the agent.

Each step follows the same cycle:

1. Run a prompt in the coding agent of your choice.
2. Inspect the files the agent created or modified.
3. Run the local checks.
4. Compute the Harness Score.
5. Compare the result with the previous step.
6. Create a commit and a checkpoint tag.

The prompts work with different coding agents. The hooks step uses the Cursor
format because it is a runtime recognized by the scanner and has a short
example. You can create and test the files with any agent. To watch the hooks
being triggered automatically, use Cursor.

## Expected progression

| Step | Capability added | Expected level | Approximate score |
| --- | --- | --- | --- |
| 0 | Working application, no harness | L0 - Unharnessed | 14/108 |
| 1 | Global context | L1 - Documented | 21/108 |
| 1B | Leaner context | L1 - Documented | 21/108 |
| 2 | Rule, skill, workflow, and hygiene | L2 - Guided | 52/108 |
| 3 | Sensors and CI | L3 - Sensing | 83/108 |
| 4 | Runtime hooks | L4 - Self-correcting | 97/108 |
| 5 | Regression gate | L4 - Self-correcting | 97/108 |

The numbers are references. LLM output can vary. If the agent creates
artifacts beyond the requested scope, the score may also vary. The main thing
is to observe the level change and the dimension that improved.

Because the tutorial runs the command without a pinned version, new releases
may add checks or change the total number of points. In that case, treat the
levels and trends as the main reference, not the exact numbers in this table.

## Prerequisites

Install:

- Git.
- Node.js 24 or newer.
- A coding agent able to edit the local repository.
- GitHub CLI, optional for creating a copy and publishing the results.

Confirm the tools:

```powershell
node --version
npm --version
git --version
gh --version
npx --yes harness-score --version
```

The tutorial uses `npx harness-score` without pinning a version, following the
version published on npm. Running the last command before you start also warms
the `npx` cache.

## Create your own copy

### Option 1 - Use the template on GitHub

Open this repository on GitHub, select **Use this template**, and create a new
repository in your account. Then clone your copy:

```powershell
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
Set-Location YOUR-REPOSITORY
```

### Option 2 - Use the GitHub CLI

```powershell
gh repo create my-harness-lab `
  --template paladini/harness-score-tutorial `
  --private `
  --clone
Set-Location my-harness-lab
```

You can swap `--private` for `--public`.

### Option 3 - Run locally only

```powershell
git clone https://github.com/paladini/harness-score-tutorial.git `
  my-harness-lab
Set-Location my-harness-lab
git remote remove origin
```

Confirm the initial state:

```powershell
git status --short
git log -1 --oneline
```

The worktree should be clean.

## Recommendations for running the prompts

- Run one prompt at a time.
- Start a new agent task for each committed step.
- Review the diff before accepting a change.
- Do not let the agent commit automatically.
- Do not jump straight to the highest score.
- Preserve the file boundaries defined in each prompt.
- Fix validation failures before creating the checkpoint.

Starting a new task at each step lets the agent load the freshly committed
harness from the beginning of the next request.

## Step 0 - Create an application without a harness

### L0 goal

Create working code before adding any agent-specific infrastructure.

### Run the L0 prompt

```text
Create the smallest useful command-line application in Node.js 24 with ESM for
this repository.

Product: Meeting Cost CLI.

Behavior:
- Take the number of participants, the meeting duration in minutes, and the
  hourly cost from the command line.
- Calculate the total labor cost of the meeting.
- Reject non-finite values, fewer than one participant, non-positive duration,
  and negative hourly cost.
- Show a clear result for valid inputs and an actionable error for invalid
  inputs.

Architecture:
- Keep the calculation in a pure, exported domain function.
- Keep argument parsing and terminal output in a separate entry point.
- Use only native Node.js features and ESM.
- Add an npm start command.
- Create PROJECT.md with a short description and a usage example.
- Preserve README.md and LICENSE without changes.

This repository must remain deliberately unharnessed at this step. Do not
create AGENTS.md, CLAUDE.md, GEMINI.md, rules, skills, workflows, hooks, tests,
test configuration, linter, formatter, typecheck configuration, CI, .gitignore,
MCP configuration, pre-commit, or a Harness Score workflow. Do not install
dependencies and do not generate package-lock.json yet.

Run the application with a valid example. Then report the files created, the
command executed, and its output. Do not commit.
```

### Verify the application

```powershell
git diff --stat
npm start -- 6 45 120
```

Check that the agent created only:

- `package.json`.
- The source directory.
- `PROJECT.md`.

### Compute the first score

```powershell
npx --yes harness-score .
```

Expected result: `L0 - Unharnessed`.

Consider this the first official scan of the tutorial. If you scan the template
before running the prompt, you may see a slightly higher score because there is
no dependency manifest yet. Once `package.json` appears without a lockfile, the
reproducible-install check becomes applicable and stays pending. Step 2 closes
that gap with `package-lock.json`.

Observe:

- The application works.
- There is no agent-specific context.
- There are no tests or automated feedback.
- There are no hooks or CI.
- Some hygiene checks pass because no credentials are exposed.

The score measures the infrastructure for agent-assisted work. It does not
measure commercial usefulness or the overall quality of the product.

### Save the L0 checkpoint

```powershell
git add package.json src PROJECT.md
git commit -m "Create raw Meeting Cost CLI"
git tag demo-l0
```

## Step 1 - Add global context

### Verbose L1 goal

Show how a context file at the root guides the agent, and also why more text
does not always mean better guidance.

### Run the verbose L1 prompt

```text
Read the current repository and create only an AGENTS.md at the root.

The file must be useful enough to guide a coding agent, but it should also
look like an overly verbose first draft generated by an LLM.

Include:
- What the product does.
- The actual repository structure.
- The commands that actually exist today.
- Domain invariants derived from the source code.
- ESM and dependency constraints.
- Validation and error-handling expectations.
- Security boundaries.
- Actions an agent must not perform.
- A completion checklist.

Use at least four Markdown headings and more than 35 non-empty lines. Repeat
some ideas in different sections so the file is clearly larger than necessary,
but do not invent commands, files, services, or requirements.

Do not change the source code, package.json, PROJECT.md, README.md, LICENSE, or
any other file. Do not create rules, skills, hooks, sensors, CI, .gitignore, or
MCP configuration. Do not commit. At the end, report the line count and the
headings of AGENTS.md.
```

### Inspect and measure the verbose L1

```powershell
git diff -- AGENTS.md
npx --yes harness-score .
```

Expected result: `L1 - Documented`.

Look at the `CTX-01` and `CTX-02` checks. The scanner found a file at the root
and verified structural criteria that reject a stub. That does not prove every
sentence is necessary or semantically correct.

A file loaded on every task consumes model context. A good harness uses the
smallest guidance that can prevent real mistakes.

### Save the verbose checkpoint

```powershell
git add AGENTS.md
git commit -m "Add verbose agent context"
git tag demo-l1-verbose
```

## Step 1B - Trim the context without chasing points

### Context reduction goal

Improve harness quality even when the metric stays the same.

### Run the context reduction prompt

```text
Refactor only the root AGENTS.md to remove repetition and reduce context cost.

Preserve every repository-specific fact an agent needs:
- Product goal.
- Actual structure.
- Actual commands.
- Domain invariants.
- Runtime dependency policy.
- Security boundaries.
- Completion criteria.

Use short sections, imperative language, and compact bullets. Keep at least 20
non-empty lines and two Markdown headings so the file stays substantial, but
make it materially smaller than the current version.

Do not add new requirements. Do not change any other file. Do not commit.
Report the number of non-empty lines before and after and summarize which
repetitions were removed.
```

### Inspect and measure the trimmed context

```powershell
git diff --stat
git diff -- AGENTS.md
npx --yes harness-score .
```

The score should stay practically the same. The harness improved because the
file uses less context and preserves the same required information.

The Harness Score is a map of structural signals. It should not be treated as
an objective function to be maximized at any cost.

### Save the final L1 checkpoint

```powershell
git add AGENTS.md
git commit -m "Tighten agent context"
git tag demo-l1
```

## Step 2 - Add scoped guidance and hygiene

### L2 goal

Distribute instructions according to when they are needed and add basic
protections to the repository.

### Run the L2 prompt

```text
Evolve this repository from documented context to agent-guided work. Create
only the harness and hygiene artifacts described below.

1. A path-scoped rule in .agents/rules/ for the source directory.
   - Use valid YAML frontmatter.
   - Include a useful description.
   - Scope it to src/** instead of loading the rule globally.
   - Record only domain and architecture rules supported by the code.

2. A skill in .agents/skills/add-calculation-case/SKILL.md.
   - Use frontmatter with name and description.
   - Write a description longer than 40 characters, phrased as a clear trigger
     condition that starts with "Use when".
   - Teach the repeatable process for adding or changing a calculation rule,
     including edge cases and verification.

3. An explicit verification workflow in .agents/workflows/.
   - Instruct the agent to run only commands that actually exist.
   - If tests, lint, or typecheck do not exist yet, record that those sensors
     are pending instead of inventing commands.

4. Repository hygiene.
   - Add .gitignore for node_modules, coverage, .env, and .env.*, allowing
     .env.example.
   - Preserve the existing LICENSE.
   - Generate a valid package-lock.json with no runtime dependencies.

Keep the root AGENTS.md compact. Do not add product features, tests, lint,
formatter, typecheck, CI, hooks, MCP, subagents, or pre-commit.

Validate that every frontmatter block can be parsed, list all created files,
and explain when the rule, the skill, and the workflow are loaded. Do not
commit.
```

### Inspect and measure the L2

```powershell
git diff --stat
npx --yes harness-score .
```

Expected result: `L2 - Guided`.

Note the role of each artifact:

- `AGENTS.md` keeps global context.
- The rule is loaded only for relevant paths.
- The skill packages a repeatable procedure.
- The workflow represents an explicitly requested action.
- `.gitignore` reduces the risk of committing dependencies, artifacts, and
  credentials.
- The lockfile makes installs reproducible.

### Save the L2 checkpoint

```powershell
git add AGENTS.md .agents .gitignore package-lock.json
git commit -m "Add scoped agent guidance and hygiene"
git tag demo-l2
```

## Step 3 - Add sensors and CI

### L3 goal

Turn claims about quality into computable evidence.

### Run the L3 prompt

```text
Add the smallest real feedback system for this Node.js 24 repository.

Use these development tools with pinned versions:
- @biomejs/biome 2.5.3 for lint and formatting.
- typescript 5.9.3 for strict typecheck with checkJs and no emitted files.
- @types/node 24.13.3 for the Node.js APIs.
- The native Node.js test runner for the tests.

Required outcomes:
- Add JSDoc types wherever the current JavaScript needs them.
- Add tests for valid calculation, rounding, invalid ranges, and non-finite
  inputs.
- Add a strict tsconfig.json that checks the JavaScript source and tests.
- Add a valid biome.json with lint and formatting enabled.
- Add npm scripts named test, lint, format, typecheck, and check.
- Make check run lint, typecheck, and tests.
- Update the verification workflow and AGENTS.md to reference the real
  commands, without duplicating procedural details.
- Add .github/workflows/ci.yml for pushes to main and pull requests.
- In CI, use Node.js 24 and run npm ci, npm run lint, npm run typecheck, and
  npm test as explicit steps.
- Give the workflow read-only contents permission only.

Do not add hooks, a Harness Score workflow, MCP, subagents, pre-commit, runtime
dependencies, deploy, or unrelated features.

Install the pinned development dependencies, run the formatter, run
npm run check, and fix every failure. Report the number of tests and the lint
and typecheck results. Do not commit.
```

### Run the L3 checks

```powershell
npm run check
git diff --stat
npx --yes harness-score .
```

Expected result: `L3 - Sensing`.

Notice how each sensor reduces a class of uncertainty:

- Tests verify behavior.
- Lint verifies mechanical conventions.
- Typecheck covers every analyzable path.
- The formatter reduces noise in diffs.
- CI repeats the checks outside the author's session.

The agent no longer just claims it is done. It can provide reproducible
evidence.

### Save the L3 checkpoint

```powershell
git add AGENTS.md .agents package.json package-lock.json src test `
  tsconfig.json biome.json .github/workflows/ci.yml
git commit -m "Add deterministic sensors and CI"
git tag demo-l3
```

## Step 4 - Add runtime hooks

### L4 goal

Move critical rules from prose instructions to mechanisms executed outside the
model.

### Run the L4 prompt

```text
Close the agent's feedback loop with the smallest meaningful set of Cursor
hooks.

Create .cursor/hooks.json with version 1 and exactly two known events:

1. beforeShellExecution as a gate hook.
   - Reference a dependency-free Node.js script committed in .cursor/hooks/.
   - Parse the JSON payload received on stdin and return valid JSON on stdout.
   - Allow common commands.
   - Deny npm publish, git push --force, git reset --hard, recursive removal of
     a root or home directory, and destructive PowerShell Remove-Item patterns.
   - Return ask when the payload cannot be parsed instead of silently allowing.

2. afterFileEdit as a feedback hook.
   - Reference a Node.js script committed in .cursor/hooks/.
   - Read the edited file path from the hook payload.
   - Run the local Biome formatter only for supported JavaScript and JSON
     files.
   - Keep formatting as guidance so CI remains the source of truth.

Keep both scripts fast and local to the repository. Do not add other hooks,
MCP, subagents, pre-commit, or product features.

Add automated tests focused on the gate's allow, deny, and malformed-payload
behaviors. Run npm run format and npm run check. Then manually exercise an
allowed command payload and a denied payload with npm publish and report the
JSON responses. Do not commit.
```

### Run the L4 checks

```powershell
npm run check
git diff --stat
npx --yes harness-score .
```

Expected result: `L4 - Self-correcting`.

Compare two kinds of control:

- A prose rule asks the model to avoid an action.
- A gate hook intercepts and blocks the action before execution.

The feedback hook runs the formatter while the edit is still in context. The
gate hook protects high-impact operations even when an instruction is ignored
or misunderstood.

L4 does not mean perfection. It represents defense in depth: guidance reduces
mistakes, sensors expose mistakes, hooks block known classes of dangerous
actions, and CI verifies the results again.

### Save the L4 checkpoint

```powershell
git add .cursor package.json package-lock.json test
git commit -m "Add runtime gate and feedback hooks"
git tag demo-l4
```

## Step 5 - Prevent harness regression

### Maturity gate goal

Make GitHub Actions fail when harness maturity drops below L4.

### Run the maturity gate prompt

```text
Add a dedicated GitHub Actions workflow that prevents silent regression of this
repository's AI harness.

Requirements:
- Create only .github/workflows/harness-score.yml.
- Run on pushes to main and pull requests.
- Use read-only contents permission.
- Use actions/checkout@v7.
- Use paladini/harness-score@v1.
- Do not set the `version` input, so the command runs without a pinned
  version.
- Set min-level to 4.
- Disable badge generation for this exercise.
- Keep this workflow separate from the product CI workflow to make it explicit
  that one pipeline tests the product and the other tests the harness.

Do not change application code, existing CI, hooks, or any other file.
Validate the YAML and run the equivalent local gate with
`npx --yes harness-score . --min-level 4`. Do not commit.
```

### Verify the gate

```powershell
npx --yes harness-score . --min-level 4
git diff -- .github/workflows/harness-score.yml
```

The product workflow verifies the code. The Harness Score workflow verifies the
agent infrastructure. If someone removes the hooks, maturity drops and the pull
request fails.

The score stops being a snapshot and starts working as a ratchet: the
repository can improve, but it does not silently regress.

### Save the checkpoint and publish

```powershell
git add .github/workflows/harness-score.yml
git commit -m "Gate harness maturity in CI"
git push origin main --follow-tags
gh run list --limit 10
```

If you are working locally only, skip the last two commands.

## Compare scores with diff mode

Before running a step, save the report outside the repository:

```powershell
$reports = Join-Path (Split-Path -Parent $PWD) "meeting-cost-demo-reports"
New-Item -ItemType Directory -Force $reports
npx --yes harness-score . --quiet --json > "$reports/before.json"
```

After running the prompt:

```powershell
npx --yes harness-score . --diff "$reports/before.json"
```

The report shows:

- Level change.
- Total score variation.
- Dimensions that changed.
- Newly passing checks.
- Checks that regressed.

## Review your checkpoints

```powershell
git tag --list "demo-*"
git log --oneline --decorate --graph --all
```

Checkpoints let you review the evolution without relying on memory or on the
conversation history with the agent.

Avoid destructive Git commands. If you want to compare an earlier step, use
`git show`, `git diff`, or a second clone of the repository.

## Recover a step that went out of scope

If the agent creates files beyond what was requested, run this prompt before
continuing:

```text
Compare the current uncommitted changes with the exact file boundaries defined
for the current step in README.md. Remove only the files and changes created in
this step that fall outside those boundaries. Preserve every already committed
file and all work created by the user.

Then rerun the step's validation commands and report the remaining diff. Do
not commit.
```

## Troubleshoot common problems

### The score is higher than expected

The agent probably created artifacts from future steps. Run `git diff --stat`,
compare the files with the prompt's boundary, and use the recovery prompt.

### The score did not change

Read the checks that are still failing. Confirm names, paths, frontmatter,
JSON, and CI commands. The Harness Score shows evidence and remediation for
each check.

### `npx` cannot find the executable on Windows

Confirm Node.js and npm, warm the cache with the version command, and run the
scan with an absolute path to the current directory:

```powershell
npx --yes harness-score "$PWD"
```

### The hooks are not triggered automatically

The files can be created and tested with any agent, but the hook runtime is
tool-specific. Open the project copy in Cursor to test the
`beforeShellExecution` and `afterFileEdit` events automatically.

### CI does not recognize tests or lint

Use explicit commands as separate steps in the workflow. A single generic
command can hide from the scanner which sensors actually run.

## Next steps

Use the remaining gaps as questions, not as an automatic to-do list:

- Is there a repeated procedure that deserves a new skill?
- Could a recurring mistake become a scoped rule?
- Is there a verifiable property that deserves a new sensor?
- Does any high-impact action need confirmation or blocking?
- Would a subagent solve a real delegation need?
- Does the project really need MCP or pre-commit?

Do not add artifacts just to reach 108 points. A smaller harness that matches
the project's risks is more useful than a decorative collection of files.

## References

- [Harness Score repository](https://github.com/paladini/harness-score).
- [Measure and improve guide](https://paladini.github.io/harness-score/guide/measure-and-improve).
- [Maturity model](https://paladini.github.io/harness-score/guide/maturity-model).
- [Portuguese guide](https://paladini.github.io/harness-score/pt-BR/).

## License

This tutorial project is distributed under the MIT license. See
[`LICENSE`](LICENSE).

# manfen-satck

Agent skills extracted from pstack. Each skill is a `skills/<name>/SKILL.md` file with YAML frontmatter (`name`, `description`), plus optional references, scripts and assets loaded on demand.

**58 skills** in total.

## Workflow & orchestration

| Skill | What it does |
| --- | --- |
| [`architect`](skills/architect/SKILL.md) | Sketch types, signatures, and module structure before code, then stay in the loop while implementation fills in. Use for /architect, 'architect this', 'design this', or non-trivial work where jumping to code would lock in the wrong shape. |
| [`arena`](skills/arena/SKILL.md) | Spawn N parallel candidates at the same task, pick a base, graft the strongest parts of the losers into it. Use for /arena, 'arena this', 'throw it in the arena', or when one attempt at a non-trivial artifact would lock in the wrong shape. |
| [`figure-it-out`](skills/figure-it-out/SKILL.md) | Design an auditable playbook when no narrower one fits: a large migration, an ambitious multi-part change, or work a human reviews after stepping away. Scales rigor to the task, runs a hypothesis loop, and logs decisions. Use for /figure-it-out or 'figure it out'. |
| [`swarm`](skills/swarm/SKILL.md) | Fan out N parallel workers, drain them, and return one report. Use for /swarm, 'swarm this', or parallel coverage, races, gauntlets, and exploration. |
| [`show-me-your-work`](skills/show-me-your-work/SKILL.md) | Keep a reviewable decision trail for long-running or unattended work: a TSV log with one row per decision (what, why, evidence, result). Local by default; commit it when a reviewer needs the trail to trust the result. Use for /show-me-your-work, autonomous or multi-phase runs, or work a human reviews after stepping away. |
| [`sharpen`](skills/sharpen/SKILL.md) | Make a task deeper, not bigger, then rewrite the prompt. Use for /sharpen. |
| [`recall`](skills/recall/SKILL.md) | Reconstruct your recent working context from your own chat history, live state, and the shared record (user reports, prior fixes, incidents), then hand back a tight current-state brief. Use for 'recall my work on X', 'catch me up', 'what have I been working on', 'where did I leave off', before starting or resuming work. |
| [`reflect`](skills/reflect/SKILL.md) | Spawn three parallel review subagents over the active transcript, surface learnings, and route each to a concrete edit on an existing skill. Use when the user says reflect. |
| [`correct`](skills/correct/SKILL.md) | Find the mistakes agents keep repeating in this repo and make each one impossible. Try architecture first, then types, then a lint whose error names the fix, then a test, and write docs last. Prove each check fails on a real past mistake. Repeat this each time the operator corrects you. Use for /correct. |
| [`tdd`](skills/tdd/SKILL.md) | 仅在用户明确要求 TDD、失败测试或回归测试时使用，或当 bug 有明显且廉价的本地测试目标时使用。测试路径不清、成本高、偏集成或未请求时跳过。 |
| [`automate-me`](skills/automate-me/SKILL.md) | Use for \"automate me\", \"create/update/refresh my -mode skill\", \"turn/capture my preferences or working style into a skill\", or wanting agents to follow how the user works. Drafts or revises a personal -mode skill via create-skill + unslop, optionally pulling fresh evidence from recent transcripts. |

## Review & verification

| Skill | What it does |
| --- | --- |
| [`blast-radius`](skills/blast-radius/SKILL.md) | Find what a change could break somewhere else before it ships, beyond the diff, and prove the one fact it's safe because of by running real code instead of writing it up. Use for 'blast radius of X', 'what could this break', or reviewing a small diff you don't trust. |
| [`interrogate`](skills/interrogate/SKILL.md) | Use for \"interrogate\", \"adversarial review\", \"multi-model review\", \"challenge this\", \"stress test this code\", \"find blind spots\", or \"tear this apart\". Adversaries try to break a change or design, and only findings proven by a run survive. |
| [`benchmark-checklist`](skills/benchmark-checklist/SKILL.md) | Vet a perf measurement (limiter, tuning, limits, errors, repeatability, relevance, and whether the work happened) before you report or act on it. Use when you run a benchmark or report a speedup or regression you measured. |
| [`create-verification-skill`](skills/create-verification-skill/SKILL.md) | Generate a project-local verification skill that drives your app the way a user does — any language, framework, or platform. Use for /create-verification-skill, \"make a control skill for this repo\", or when a project has no scripted way to prove UI/CLI/service behavior. |
| [`maintain-verification-skill`](skills/maintain-verification-skill/SKILL.md) | Periodic pass that keeps a project's verification skill and feature map honest: parallel source readers per feature, one live session driving every feature, at most one PR of proven corrections. Use for /maintain-verification-skill or \"audit the verify skill\". |
| [`create-formal-verification-skill`](skills/create-formal-verification-skill/SKILL.md) | Generate a project-local skill that formally verifies the code where a bug costs the most and tests reach the least, in any language, with whichever verification tool fits. Use for /create-formal-verification-skill, \"model-check this race\", \"prove this invariant\", or when concurrency, retries, state machines, parsers, or unsafe code need more than tests. |
| [`maintain-formal-verification-skill`](skills/maintain-formal-verification-skill/SKILL.md) | Bring a project's formal verification back in line with the code after the code changes: find the commits that touched verified code, update the model, harness, or properties to describe the code as it is now, rerun, and say whether each failure is drift in the verification or a bug in the code. Use for /maintain-formal-verification-skill, \"is the model still true to the code\", or after merges and refactors that touched verified code without rechecking it. |
| [`statechart`](skills/statechart/SKILL.md) | Check a TLA+ model of a system that moves between states, such as a product flow, a protocol, or a service, and draw it as a Mermaid statechart. The report gives the shortest path to each state that breaks a rule. Use for /statechart, or to show a bug as a path through states. |
| [`no-comments`](skills/no-comments/SKILL.md) | Spawn Comment Sicko, fix accepted findings, and offer encodings for claimed constraints. |

## Understanding & explanation

| Skill | What it does |
| --- | --- |
| [`how`](skills/how/SKILL.md) | 用于「X 怎么工作」、改代码前的走读，以及归属 / 分层问题（「该放哪」「哪个包管这个」「这是不是正确的层」）。解释子系统架构、运行时流程、上手心智模型。动机类问题用 why。 |
| [`why`](skills/why/SKILL.md) | Use for 'why does X work this way', 'why we picked Y', design rationale, regressions, postmortems, or data-backed thresholds. Discovers available MCPs and queries each evidence category (source control, issue tracker, long-form docs, real-time chat, infrastructure observability, error tracking, product analytics warehouse) in parallel, then returns a cited read on decisions and tradeoffs. Use how for runtime behavior. |
| [`teach`](skills/teach/SKILL.md) | Explain a body of work plainly so a person actually understands it. Runs the `how` and `why` skills and weaves what they find into one clear explanation. Use for 'teach me this', 'help me really understand X', 'explain this change or subsystem to me'. |
| [`bro`](skills/bro/SKILL.md) | Restate the last message in plain human language, with no jargon. |
| [`in-your-own-words`](skills/in-your-own-words/SKILL.md) | Restate in your own words what you think the user's goals are and what problem they're trying to solve. Use for /in-your-own-words. |
| [`ketchup`](skills/ketchup/SKILL.md) | Catch the user up on what changed in this chat since their last message, in plain words and small visuals. List every item waiting on them with what they need to decide it from the reply alone. Use for /ketchup. |
| [`ketchup-video`](skills/ketchup-video/SKILL.md) | Catch the user up like /ketchup, with a short subtitled video of slides for what happened and the waiting calls as text under it. Use for /ketchup-video. |

## Writing & style

| Skill | What it does |
| --- | --- |
| [`unslop`](skills/unslop/SKILL.md) | Cut AI tells from any writing. |
| [`technical-writing`](skills/technical-writing/SKILL.md) | Layered technical-writing standard: Diátaxis structure, Google developer style sentences, STE instruction rules, Global English syntax. Use for /technical-writing or when writing or reviewing docs, RFCs, readmes, PR descriptions, or commit messages. |
| [`poteto-mode`](skills/poteto-mode/SKILL.md) | poteto's agent style for concise, detailed responses, deliberate subagents, unslopped prose, simple code, and verified work. Use for poteto, /poteto-mode, or requests to work in this style. |
| [`poteto-help`](skills/poteto-help/SKILL.md) | Guides users through pstack setup, /poteto-mode, and picking the skill, playbook, or principle for a task. Type /poteto-help with a question. |

## Setup & language

| Skill | What it does |
| --- | --- |
| [`setup-pstack`](skills/setup-pstack/SKILL.md) | Configure which models pstack uses per role and at what reasoning budget. Detects your available models and writes an always-applied rule that overrides the skill defaults. Use for /setup-pstack, "configure pstack models", "pstack budget", or changing pstack's model choices. |
| [`typescript-best-practices`](skills/typescript-best-practices/SKILL.md) | TypeScript best practices. Use when reading or editing any .ts or .tsx file. |
| [`make-bot-ui`](skills/make-bot-ui/SKILL.md) | Use when building a custom UI (page, dashboard, buttons) that should wake a Grok Bot over a webhook, when the user must provide a webhook sender key, or when exposing that UI on Tailscale. |

## Principles

Small always-on principles: each states the situation that triggers it and the move it prescribes.

| Skill | What it does |
| --- | --- |
| [`principle-attack-the-premise`](skills/principle-attack-the-premise/SKILL.md) | Apply when two or more fixes that share one premise have failed the same gate. Write the premise down and test it directly before you write another fix that assumes it. |
| [`principle-boundary-discipline`](skills/principle-boundary-discipline/SKILL.md) | Apply when wiring validation, error handling, or framework adapters. Concentrate guards at system boundaries (CLI, config, network, external APIs); trust internal types and keep business logic in pure functions. |
| [`principle-build-the-lever`](skills/principle-build-the-lever/SKILL.md) | Apply to any non-trivial work, not just bulk work: edits, migrations, analyses, checks. Build the tool that does it or proves it (codemod, script, generator, or a skill your subagents follow) instead of working by hand. The tool is the artifact a reviewer can rerun. |
| [`principle-encode-lessons-in-structure`](skills/principle-encode-lessons-in-structure/SKILL.md) | Apply when you catch yourself writing the same instruction a second time, or notice a recurring correction. Encode the rule as a lint, metadata flag, runtime check, or script instead of more text. |
| [`principle-exhaust-the-design-space`](skills/principle-exhaust-the-design-space/SKILL.md) | Apply when facing a novel UI interaction or architectural decision with no precedent in the codebase. Build 2-3 competing prototypes and compare side by side before committing. |
| [`principle-experience-first`](skills/principle-experience-first/SKILL.md) | Apply when product, UX, or feature-scope tradeoffs come up. Choose user delight over implementation convenience; ship fewer polished features over more rough ones. |
| [`principle-explain-the-number`](skills/principle-explain-the-number/SKILL.md) | Apply before you measure, and before you trust, report, or act on a number you measured: a speedup, a regression, a throughput, a latency, or an eval result. Find what limits it, and rule out that it measured something other than the work you think. |
| [`principle-fix-root-causes`](skills/principle-fix-root-causes/SKILL.md) | Apply when debugging. Trace each symptom back to the first place the system went wrong and fix it there. A guard is a fix only when the state it handles is legitimate. |
| [`principle-foundational-thinking`](skills/principle-foundational-thinking/SKILL.md) | Apply before writing logic: choosing core types and data structures, sequencing scaffold-vs-feature work, asking what concurrent actors share. Get the data structures right so downstream code becomes obvious. |
| [`principle-guard-the-context-window`](skills/principle-guard-the-context-window/SKILL.md) | Apply when context is filling up: large outputs, long files, repeated reads, fan-out planning. Route bulk to subagents; keep summaries in the main thread, not raw payloads. |
| [`principle-laziness-protocol`](skills/principle-laziness-protocol/SKILL.md) | Apply when refactoring, evaluating diff size, or tempted to add abstractions, layers, or signal threading. Bias toward deletion and the smallest change that solves the problem. |
| [`principle-make-operations-idempotent`](skills/principle-make-operations-idempotent/SKILL.md) | Apply when designing commands, lifecycle steps, or processing loops that run amid crashes, restarts, and retries. Converge to the same end state regardless of partial prior runs. |
| [`principle-migrate-callers-then-delete-legacy-apis`](skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | Apply when introducing a new internal API while old callers still exist. Migrate callers and delete the old API in the same wave instead of preserving compatibility layers. |
| [`principle-minimize-reader-load`](skills/principle-minimize-reader-load/SKILL.md) | Apply when reviewing or shaping code that's hard to trace. Count layers between question and answer, and hidden state in the reader's head; collapse one-caller wrappers and shrink mutable scope. |
| [`principle-model-the-domain`](skills/principle-model-the-domain/SKILL.md) | Apply when writing stateful logic, or when code branches a lot or repeats a shape assumption across files. |
| [`principle-never-block-on-the-human`](skills/principle-never-block-on-the-human/SKILL.md) | Apply when tempted to ask 'should I do X?' on reversible work. |
| [`principle-outcome-oriented-execution`](skills/principle-outcome-oriented-execution/SKILL.md) | Apply during planned rewrites and migrations with explicit phase boundaries. Converge on the target architecture; don't preserve smooth intermediate states with throwaway compatibility code. |
| [`principle-prove-it-works`](skills/principle-prove-it-works/SKILL.md) | Apply after completing a task, before declaring done. Verify against the real artifact (run the feature, read the actual value, inspect the diff), not a proxy, self-report, or 'it compiles. |
| [`principle-redesign-from-first-principles`](skills/principle-redesign-from-first-principles/SKILL.md) | Apply when integrating a new requirement into an existing design. |
| [`principle-separate-before-serializing-shared-state`](skills/principle-separate-before-serializing-shared-state/SKILL.md) | Apply when concurrent actors might write to the same file, branch, key, or state object. Eliminate the sharing first; serialize structurally only when one shared writer is a real invariant. |
| [`principle-sequence-verifiable-units`](skills/principle-sequence-verifiable-units/SKILL.md) | Apply to multi-step work (sweeps, migrations, runs of similar edits) and to how you stack commits and PRs. Break work into small units that each end in a verifiable state, check each before the next, and order delivery so the sequence proves itself to a reviewer. |
| [`principle-subtract-before-you-add`](skills/principle-subtract-before-you-add/SKILL.md) | Apply when sequencing an addition, refactor, or rewrite. Remove dead code, redundant validators, and stub references first, then build on the simpler base. |
| [`principle-test-behavior-not-implementation`](skills/principle-test-behavior-not-implementation/SKILL.md) | Apply when you write, change, or keep a test. Assert what the code's users can observe, so that the test fails when that behavior breaks and keeps passing when only the internals change. |
| [`principle-type-system-discipline`](skills/principle-type-system-discipline/SKILL.md) | Apply when designing types, reviewing a function signature, or writing code in any statically-typed language. Make illegal states unrepresentable, brand semantic primitives, parse external data at boundaries, exhaust variants, derive from authoritative schemas. |

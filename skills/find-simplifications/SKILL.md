---
name: find-simplifications
description: Use when working in a codebase to find non-obvious simplification candidates, write proposed design notes or inline TODO/FIXME/XXX notes, or fold worthwhile simplification ideas from another PR; especially for dead, duplicated, speculative, over-built, added-then-removed, or hand-rolled-where-a-dependency-exists surfaces.
metadata:
  source: https://github.com/deepseek-ai/deepseek-harness
  upstream_path: .agents/skills/dsh-find-simplifications/SKILL.md
  adapted_by: ai-vibe-coding
  invocation: model
---

# Finding Simplifications

This skill helps turn a broad "find things to simplify" request into evidence-backed proposals that remove or collapse existing surface area. It is guidance, not a checklist: follow the code, keep judgment active, and prefer a few well-proven candidates over a pile of thin guesses.

## Start With Repo Context

- Read `AGENTS.md`, `CONTRIBUTING.md`, or equivalent conventions, especially the pre-release stance and the conventions (including the tests-are-not-golden-truth and design-notes-are-not-golden-truth doctrines), plus `docs/defensive-patterns.md` and `docs/testing.md` if available.
- Skim `docs/architecture.md` or equivalent before judging anything in the codebase; simplifications that fight the service map or event taxonomy need extra evidence.
- Use design notes / ADRs and their rules to understand intentional architecture. Treat recorded design decisions as settled unless new evidence beats the recorded rationale.
- Treat dual adapters and dual persistence backends as intentional by default. Do not propose deleting either twin/backend as "low effort" unless the user explicitly overrides that constraint. Removing an unused method or hook inside a protected seam can still be valid if it does not collapse the protected design.

## What Counts As A Strong Candidate

A strong simplification removes, folds, or demotes something real and has clear evidence that the current design costs more than it buys:

- A public method, event, config knob, registry notification, helper, package, durable event, or test artifact has no production consumer.
- Tests or docs are the only consumers, and the behavior they pin is not load-bearing.
- Two representations mirror the same fact, especially across durable session events and transient events.
- A seam has methods every implementation must support but no consumer uses.
- A separate package exists only for test/demo/support code and adds publish or dependency overhead.
- A feature implements speculative product generality: multi-session/session-load, background job rosters, live registry invalidation, mid-turn steering, tool-owned UI rendering, and similar designs with no product owner.
- An invariant, rollback path, set of expected outputs, or special-case test exists only to protect an unused API.
- Hand-rolled code reimplements what a well-maintained external package or a Node builtin at the engine floor already provides, and the swap would delete the implementation plus its dedicated tests.
- The simplified behavior may differ slightly, but the new behavior is still reasonable and easier to explain.

Thin candidates are usually not enough: deleting one typo, running `knip` once, removing an intentionally documented backend/adapter, or flagging "this looks complex" without call-site proof.

## Survey Broadly

Use parallel subagents when the user asks for breadth or many candidates. Give each agent a domain and require evidence, not guesses. Useful domains:

- Agent loop and session log: turn/step boundaries, steering, abort/cancel, durable events, replay, load/resume.
- Automation and human UI APIs: prompt settlement and teardown on the protocol side; transcript rendering and interaction state on the UI side.
- LLM/tools/system prompt: stream/generate APIs, assemblers, registries, tool schema defaults, presentation hooks.
- Bash and tool execution: foreground/background split, job ownership, output spill files, executor methods.
- Packages/examples/scripts/tests: package splits, static inventories, redundant snapshot expected outputs, support packages.

If subagents are unavailable, simulate the same breadth yourself. Do not let the first good candidate stop the survey.

Start with the largest production-code deltas. A broad simplification audit that stops after obvious unused symbols can miss the files where duplicated lifecycle or defensive machinery carries most of the cost.

## Audit Trust And Lifecycle Boundaries

For every defensive copy, freeze, validator, and callback capture, name where the value came from and who owns it next. Same-process typed service/plugin calls ordinarily borrow readonly values; parsers, config loaders, queues, model/tool JSON, durable files, workers, processes, and wire decoders own or validate their data. Tests built around hostile getters, fake typed objects, callback replacement, or mutation after a same-process handoff are evidence of a potentially speculative contract, not automatic justification for keeping it.

For complex asynchronous code, draw the ownership graph and map each sentinel, readiness promise, cancellation path, disposer, and state flag to a distinct owner or transition. When several mechanisms mirror the same liveness or settlement fact, propose one transaction or lifecycle controller instead. Preserve separate machinery where it protects synchronous publication and rollback, callback containment, first-terminal-outcome arbitration, worker/process ownership, or dispose-to-quiescence.

## Hand-Rolled Code Versus A Dependency

Introducing a dependency is a valid simplification move, not a policy exception. When surveying, ask of protocol parsers, framers, retry/backoff loops, glob matchers, diff engines, and similar infrastructure: does a well-maintained npm package or a Node builtin at the repo's engine floor already do this?

Prove a dependency-swap candidate like any other, plus:

- Read the hand-rolled implementation and name the exact surface the package covers; residual semantics the package does not cover count against the swap and stay in the proposal.
- Check the package's health honestly (maintenance, adoption, transitive footprint) and prefer builtins when the engine floor has them.
- Check design notes / ADRs first: recorded seams are settled — a swap that collapses one needs to beat the recorded rationale, not just cite the policy.
- Weigh net deletion: implementation plus dedicated tests plus docs, minus the glue that remains. A wrapper that relocates the same complexity is not a win.

## Prove Or Reject Each Candidate

For every symbol or behavior, classify consumers before writing:

- Production corpus: `src/`, `examples/`, runtime scripts, and loader/config paths.
- Non-production corpus: tests, README/docs, design notes, snapshots, generated expected outputs, and comments.
- Ambiguous corpus: examples and scripts that may be product smoke paths. Inspect usage before classifying.

Use `rg` first. Good searches include the exact symbol, event name, package name, config key, method name with both `.name(` and `name(`, and any wire strings. Then read the call sites. `knip` can help, but it is not a substitute for understanding public interfaces, dynamic event names, tests, docs, and loader paths.

Reject or downgrade a candidate when:

- A production caller exists and the simplification would be a feature decision rather than a cleanup.
- The API is explicitly justified by a design note or a hard-won defensive pattern, and the new evidence does not beat that reason.
- The removal would force unrelated churn without actually reducing the public API or required behavior.
- The idea is correct but tiny. Add a targeted TODO/FIXME/XXX instead, using the urgency semantics in `docs/development.md` or equivalent.

## Inline TODO Notes

Use inline TODO/FIXME/XXX only for small, local cleanups that are clearly useful but not durable design decisions. Keep them short and actionable:

- Name the smell with a stable tag, e.g. `TODO(double-default)` or `XXX(unused-default)`.
- Explain why it is safe to revisit and what action would simplify it.
- Do not add TODOs for speculative complaints or for behavior that needs a design-note-level decision.

## When Folding Another PR Or Branch

Diff the sibling branch against `origin/master`, not against the current PR branch, so you see its independent contribution. For each item:

- Port non-overlapping design notes or TODOs that meet the quality bar.
- Consolidate overlapping material into the existing design note that owns the topic.
- Do not port duplicate or lower-confidence proposals just to preserve the count.
- Update the PR body so reviewers see the true candidate count and scope.
- Close the duplicate PR only when the user asked you to, or when you clearly own that housekeeping.

## Validation And PR Hygiene

For docs-only work, run at least doc-sync, lint, and `git diff --check`. For code comments or skill changes, also run the relevant validator when one exists. Select any other evidence from the outgoing diff.

When opening or updating a PR, summarize:

- How many design notes and inline notes were added, consolidated, retained as partial supersessions, or deleted.
- The main areas surveyed.
- What was intentionally excluded.
- Which checks passed.

For each consolidation group, name the old and current owners, state the evidence for full supersession, and explain why deletion is safe. If an added-then-removed scan finds no qualifying note, report that result and the representative partial cases retained.

Use a draft PR while the survey is still expanding; mark ready only when the candidate set, review responses, and validation are settled.

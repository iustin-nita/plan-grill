# Evaluation results

`plan-grill` was tested against a no-skill baseline on 3 realistic planning prompts (see [`evals.json`](evals.json)). Each run was graded on 5 objective assertions:

1. Asks ≥1 structured question with 2+ concrete, mutually-exclusive labeled options (not open-ended prose)
2. Every question marks a recommended option with reasoning
3. Each option states an explicit tradeoff (gain vs. give up)
4. Questions/options are grounded in the actual codebase (real files, hooks, values)
5. Produces a concrete implementation plan with file references and a build sequence

## Scores

| Eval | With plan-grill | Baseline (no skill) |
|------|:---:|:---:|
| report-broken-fountain | 5/5 | 2/5 |
| grill-existing-plan | 5/5 | 4/5 |
| ambiguous-perf | 5/5 | 2/5 |
| **Total** | **100% (15/15)** | **53% (8/15)** |

## Reading the result

Assertions 4 (codebase grounding) and 5 (produces a plan) pass for **both** arms — a capable model already does those. The skill moves the needle on assertions 1–3: turning vague "what do you think?" questions into **labeled options with honest tradeoffs and a recommendation**. That is the skill's core contribution.

## Caveat

These runs were executed in a non-interactive harness, so they grade the *content and structure* of the questions the skill produces, not the live `AskUserQuestion` click-through UI (which can't complete without a human). The interactive behavior is the skill's defining feature; the evals verify everything that surrounds it.

The trigger-phrase set in [`trigger_eval.json`](trigger_eval.json) is included for reference (should-fire vs. near-miss queries used while tuning the skill's `description`).

# plan-grill

> Before you build, get grilled. A Claude Code / Agent Skill that surfaces the real decisions in a task and resolves them with you through structured multiple-choice questions — each with concrete options, honest tradeoffs, and a recommended answer — then turns your choices into a concrete implementation plan.

Most weak plans don't fail on coding ability. They fail because nobody resolved the decisions that actually shape the implementation. `plan-grill` front-loads that: it explores your codebase, finds the forks that matter, and asks them as clean choices — not vague "what do you think?" prose.

## Install

```bash
npx skills add iustin-nita/plan-grill
```

Then trigger it in Claude Code / Claude:

```
make a plan for adding offline mode — grill me first
```

## What makes it different

Compared to a plain "interview me" prompt, plan-grill:

1. **Asks through the structured-choice UI** (`AskUserQuestion`) — clickable options, not a wall of text. This forces concrete, mutually-exclusive answers and makes deciding a single tap.
2. **Always recommends.** Every question marks a recommended option and says *why*.
3. **States real tradeoffs.** Each option spells out what you gain vs. give up.
4. **Researches first.** It reads the codebase before asking, so it never asks what the code already answers, and its options are grounded in your actual files.
5. **Batches** related questions (up to 4 per screen) and ends with a concrete, sequenced plan.

## The loop

1. **Understand & research** — read the request, explore the codebase, answer what it can on its own.
2. **Find the decisions that matter** — the genuine forks where the implementation branches.
3. **Ask** — structured options, real tradeoffs, recommended pick.
4. **Know when to stop** — quit once remaining unknowns are low-stakes.
5. **Synthesize the plan** — turn resolved answers into an implementation plan.

## When it triggers

Planning, designing, or scoping a feature/change/migration with open decisions; "grill me"; vet / stress-test / poke holes in a plan before building; "ask me questions first", "give me options with tradeoffs".

It deliberately stays out of the way when you've already decided and just want it built, for pure debugging or code explanation, for plain ship checklists, or for non-engineering planning.

## Evaluation

The skill was benchmarked against a no-skill baseline on 3 realistic planning prompts, graded on 5 objective assertions (structured options, recommended pick, per-option tradeoff, codebase grounding, concrete plan). See [`evals/`](evals/).

| Configuration | Pass rate |
|---|---|
| **With plan-grill** | **100%** (15/15) |
| Baseline (no skill) | 53% (8/15) |

The skill's value concentrates exactly where the baseline falls short: delivering labeled options with tradeoffs and a recommendation, rather than open-ended prose questions. (Grounding the plan in the codebase and producing a plan at all — the baseline already does those well.)

## License

[MIT](LICENSE) © Iustin Nita

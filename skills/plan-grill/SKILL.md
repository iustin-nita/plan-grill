---
name: plan-grill
description: Before building anything, surface the real decisions and resolve them with the user through structured multiple-choice questions (via the AskUserQuestion tool) — each with concrete options, honest tradeoffs, and a recommended answer with reasoning — then synthesize the answers into a concrete implementation plan. Use this skill proactively whenever the user wants to plan, design, or scope a feature, change, or migration that still has open decisions; asks to be "grilled" or questioned; wants to vet, stress-test, pressure-test, or poke holes in a plan before implementation; or says things like "make a plan", "ask me questions first", "what do you need to know", "give me options with tradeoffs", or "help me think this through". Prefer this over jumping straight to code whenever a task carries meaningful unknowns or design forks. Do NOT use it when the user has already decided and just wants the change built, for pure debugging or code explanation, for plain ship/release checklists with no design forks, or for planning outside this codebase (e.g. a marketing campaign) or grilling that isn't about the user's own build (e.g. prepping someone for an interview).
---

# Plan Grill

Most weak plans fail not because the model can't code, but because nobody resolved the decisions that actually shape the implementation. This skill front-loads that resolution: find the forks that matter, put them to the user as clean choices, and only then write the plan.

The signature move is **how** you ask. Plain-text "what do you think?" questions are slow and vague — they push the work back onto the user. Instead, deliver every question through the **`AskUserQuestion` tool**, which renders clickable options. This forces you to do the hard part (propose concrete, mutually-exclusive answers with real tradeoffs) and makes the user's part a one-tap decision. Always lead with your recommendation and say why.

## The loop

1. **Understand and research first** — read the request, then explore the codebase to answer everything you can on your own.
2. **Find the decisions that matter** — identify the genuine forks where the implementation branches.
3. **Ask via AskUserQuestion** — structured options, real tradeoffs, recommended answer first.
4. **Know when to stop** — quit grilling once the remaining unknowns are low-stakes.
5. **Synthesize the plan** — turn the resolved answers into a concrete implementation plan.

---

## 1. Understand and research first

Before asking the user anything, spend your own effort. An uninformed question is worse than no question — it tells the user you didn't look. Read the relevant files, trace how the current code works, check existing conventions, dependencies, and constraints.

The rule: **never ask the user something the codebase can answer.** If the question is "which state library are we using?" — go look. Reserve the user's attention for decisions only they can make: product intent, priorities, risk appetite, things not yet written down.

When you do ask, the research pays off twice — it lets you propose *concrete* options grounded in the actual code ("store it in the existing `FavoritesContext` AsyncStorage layer" beats "use some local storage").

## 2. Find the decisions that matter

A good question targets a real fork — a point where the implementation genuinely diverges and the choice is the user's to make. Hunt for:

- **Hidden assumptions** you'd otherwise bake in silently (scope boundaries, who the feature is for, what "done" means).
- **Tradeoff forks** with no objectively correct answer — speed vs. simplicity, build vs. reuse, now vs. later, optimistic vs. pessimistic UI.
- **Ambiguity in the request** — words like "fast", "secure", "nice", "handle errors" that mean different things at different effort levels.
- **Dependencies between decisions** — choice A constrains choice B. Resolve A first.

Skip questions that are trivia (answerable by looking), cosmetic (low stakes either way), or premature (depend on an answer you don't have yet). Prioritize the few highest-leverage unknowns — the ones that, if you guessed wrong, would mean rework.

## 3. Ask via AskUserQuestion

This is the heart of the skill. Deliver questions through the `AskUserQuestion` tool, not as prose.

**Format each question:**
- **question**: the specific decision, with just enough context to decide.
- **header**: a ≤12-char chip label (e.g. "Storage", "Auth flow", "Scope").
- **options** (2–4): each a distinct, mutually-exclusive choice. Put the **recommended option first**, end its label with **"(Recommended)"**.
- **option description**: the honest tradeoff of that choice — what you gain and what you give up. For the recommended one, lead with *why* it's your default.

**Batching:** the tool takes up to 4 questions per screen. Group questions that are independent of each other into one screen so the user resolves them in a single pass. But when one answer changes what you'd ask next, ask it on its own and wait — don't batch a question whose options depend on an unanswered one.

**Always recommend.** Every question carries your pick and your reasoning. You're a collaborator with a point of view, not a survey. The user can override (they always have an "Other" escape), but they should never face a naked choice with no guidance. If you genuinely have no lean, say so in the description and explain the bet each way.

**Keep it concrete.** Options should be real, implementable paths grounded in the codebase you just read — not abstract directions. "Optimistic update, roll back on error" not "handle the UX somehow".

### Example

User: *"I want to add offline support to the map so it works underground on the U-Bahn."*

After reading the data-fetching and caching code, a strong screen of questions:

**Q1** — header `Cache scope`
question: "What should be available offline?"
- **Last-viewed area only (Recommended)** — Cheapest to build and matches the real use case: you cache tiles + features for wherever the user last was. Why recommended: covers the U-Bahn scenario without the storage cost and sync complexity of all-of-Berlin. Tradeoff: nothing offline if they jump to a fresh area.
- **All of Berlin, prefetched** — Full coverage anywhere. Tradeoff: large download, storage footprint, and a prefetch UX to manage.
- **Favorites + their surroundings** — Cache around saved fountains. Tradeoff: useless for users who haven't saved anything.

**Q2** — header `Staleness`
question: "How fresh must cached fountain data be?"
- **Serve cached, refresh in background (Recommended)** — Instant render offline, updates silently when back online. Why recommended: best perceived performance; fountain data changes rarely so staleness risk is low. Tradeoff: user may briefly see slightly old status.
- **Hard TTL, refetch when expired** — Simpler mental model. Tradeoff: blank/loading state if expired while offline.

Q1 and Q2 are independent → one screen. A question like "which TTL value?" depends on Q2's answer → ask it only if they pick the TTL path.

## 4. Know when to stop

Grilling is a means, not the goal. Stop when the remaining unknowns are low-stakes or fully resolved — when you could write the plan and any leftover choices wouldn't cause rework. Endless questioning is its own failure mode; it burns the user's goodwill and signals you can't make a call. A good session is usually one to three screens, not ten.

## 5. Synthesize the plan

Once the decisions are settled, write a concrete implementation plan that reflects every answer — the chosen approach, the files to touch, the build sequence, and any tradeoffs the user accepted (so the reasoning is recorded). The grilling exists to make this plan good; don't skip it.

If you're in plan mode, present the plan through `ExitPlanMode` for approval. Otherwise lay it out clearly and confirm before building.

---

## Anti-patterns

- **Asking what the code already answers.** Look first. Every avoidable question erodes trust.
- **Prose questions instead of the tool.** The structured UI is the whole point — it makes deciding fast and forces you to propose real options.
- **Vague options.** "Improve performance" is not a choice. "Memoize the marker layer / virtualize the list / paginate the WFS fetch" are choices.
- **No recommendation.** Never hand the user an un-opinionated menu. Lead with your pick and the why.
- **Over-grilling.** If you already have enough to plan well, plan. Stop hunting for marginal questions.
- **Fake tradeoffs.** If one option is strictly better, don't dress up the inferior one as a real alternative — just recommend and move on.

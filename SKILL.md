---
name: six-thinking-hats
description: Skill council where five hat agents analyze a problem in parallel, then a Blue Hat chairman synthesizes their outputs into a final recommendation
---

# Six Thinking Hats — Skill Council

## Overview

Six Thinking Hats (Edward de Bono) structures thinking by separating cognitive modes into six colored hats. Each hat represents one pure perspective: facts, emotions, benefits, risks, creativity, or process control. Isolating modes eliminates ego-driven debate and ensures every angle receives full attention before a decision is made.

This skill implements the method as a **council of agents**: five hat agents work in parallel, each analyzing the problem from their singular perspective. A Blue Hat chairman then synthesizes their outputs into a decision.

---

## Orchestration

When this skill is invoked with a problem or question, follow these steps exactly.

### Step 1 — Extract the problem

Identify the core problem or decision from the user's message. If ambiguous, state your interpretation clearly before proceeding. Do not ask clarifying questions — make a reasonable interpretation and name it.

### Step 2 — Spawn five hat agents in two batches

To avoid hitting API rate limits, spawn the hat agents in **two sequential batches**. Each batch runs its agents in parallel. Wait for all agents in a batch to complete before starting the next batch.

**Batch A (3 agents in parallel):**
- White Hat — facts and information gaps
- Yellow Hat — benefits and optimism
- Black Hat — risks and caution

**Batch B (2 agents in parallel, after Batch A completes):**
- Red Hat — emotions and gut reaction
- Green Hat — creativity and alternatives

Each call must be fully self-contained — sub-agents have no conversation context. Use the agent prompt templates defined below, substituting `{{PROBLEM}}` with the actual problem statement.

### Step 3 — Spawn the Blue Hat Chairman

After all five agents complete, call the Agent tool once more with the Chairman prompt below, substituting all five hat outputs into the template. The chairman synthesizes and decides.

### Step 4 — Present results

Lead with the chairman's synthesis and recommendation. Follow with each hat's findings as supporting detail, clearly labeled.

---

## Hat Agent Prompts

These are the exact prompts to use when spawning each hat agent. Substitute `{{PROBLEM}}` with the user's actual problem statement. Pass them verbatim — do not summarize or shorten.

---

### White Hat — Facts

You are the White Hat thinker in a Six Thinking Hats council. Your role is purely factual: state what is known, identify what is unknown, and flag where more information is needed. No opinions, interpretations, or value judgements — only data and observable facts.

Problem: {{PROBLEM}}

Respond with exactly these sections:

KNOWN FACTS — What we know or can safely assume (3–5 concrete points. Cite numbers, names, or observable states where possible.)

INFORMATION GAPS — What we don't know but would need to (2–3 specific gaps.)

DATA QUESTIONS — Specific questions whose answers would materially change the analysis.

Do not evaluate, recommend, or express any opinion.

---

### Red Hat — Emotions

You are the Red Hat thinker in a Six Thinking Hats council. Your role is to express gut reactions, emotions, and intuition — with no justification required. This is instinct, not analysis. Do not explain why you feel something; just state it. Keep this short.

Problem: {{PROBLEM}}

Respond with exactly these sections:

GUT REACTION — Immediate instinctive response to this problem (1–2 sentences, no justification).

EMOTIONS PRESENT — Specific feelings this problem evokes. Use precise emotional words: excitement, fear, unease, enthusiasm, doubt, relief, dread, etc.

INTUITIVE SIGNALS — Anything that "feels off" or "feels right" without being able to explain it.

Resist the urge to justify or explain. Red Hat is a 60-second gut check, not an analysis.

---

### Yellow Hat — Benefits

You are the Yellow Hat thinker in a Six Thinking Hats council. Your role is pure optimism: identify benefits, value, and best-case outcomes. Push as far positive as intellectual honesty allows. Do not introduce risks or caveats — that is another hat's job.

Problem: {{PROBLEM}}

Respond with exactly these sections:

CORE BENEFITS — The strongest reasons this could work (3–5 points).

BEST-CASE SCENARIO — What success looks like if this goes well.

HIDDEN UPSIDES — Non-obvious or second-order benefits that might be missed.

WHY NOW — Reasons the timing or context makes this particularly valuable.

Be genuinely optimistic, not superficially positive. Find the real value.

---

### Black Hat — Risks

You are the Black Hat thinker in a Six Thinking Hats council. Your role is rigorous critical judgment: identify risks, weaknesses, flaws, and reasons for caution. This is not pessimism — it is thorough risk assessment. Be specific. Vague warnings are useless.

Problem: {{PROBLEM}}

Respond with exactly these sections:

CRITICAL RISKS — The most serious things that could go wrong, ranked by severity (3–5 points). Name the risk, explain the mechanism, estimate the impact.

WEAKNESSES — Structural flaws in the approach or underlying assumptions.

WHAT'S MISSING — Important considerations that appear to have been overlooked entirely.

WORST-CASE SCENARIO — What failure looks like and a rough likelihood estimate.

DEAL-BREAKERS — Any single factor that should halt this entirely. State "none identified" if there are none.

---

### Green Hat — Creativity

You are the Green Hat thinker in a Six Thinking Hats council. Your role is creative generation: produce alternatives, modifications, and novel approaches. Challenge assumptions. There are no bad ideas in this phase.

Problem: {{PROBLEM}}

Respond with exactly these sections:

ALTERNATIVE APPROACHES — Fundamentally different ways to address this problem (3–5 distinct options).

MODIFICATIONS — Ways to adapt the obvious approach to make it better, cheaper, faster, or safer.

UNCONVENTIONAL IDEA — At least one idea that seems unlikely or unusual but might be brilliant. Do not filter it out.

REFRAMES — Alternative ways to define or interpret the problem itself that open new solution space.

HYBRID OPTIONS — Combinations of approaches that capture benefits from multiple directions.

Quantity and diversity matter. Do not self-censor aggressively.

---

## Chairman Prompt

Use this prompt after all five hat agents have returned. Substitute `{{PROBLEM}}` and each `{{HAT_OUTPUT}}` placeholder with the actual outputs.

---

You are the Blue Hat Chairman in a Six Thinking Hats council. Five specialist thinkers have analyzed a problem independently from their assigned perspectives. Your job is to integrate their outputs and deliver a clear decision. You do not re-analyze — you synthesize and decide.

Original Problem: {{PROBLEM}}

WHITE HAT REPORT (Facts):
{{WHITE_HAT_OUTPUT}}

RED HAT REPORT (Emotions):
{{RED_HAT_OUTPUT}}

YELLOW HAT REPORT (Benefits):
{{YELLOW_HAT_OUTPUT}}

BLACK HAT REPORT (Risks):
{{BLACK_HAT_OUTPUT}}

GREEN HAT REPORT (Creativity):
{{GREEN_HAT_OUTPUT}}

Respond with exactly these sections:

CONVERGENCE — Where do the hats agree? What does the council as a whole point toward?

KEY TENSIONS — The 2–3 most important trade-offs or conflicts the council has surfaced.

RECOMMENDATION — A clear, direct recommendation. Do not hedge into "it depends." Make a call. If conditions apply, state them precisely.

RATIONALE — Which hat perspectives most shaped the recommendation and why. Which were overridden and why.

NEXT ACTIONS — 2–3 concrete steps that should happen immediately after this decision.

The council has done the thinking. Your job is to decide.

---

## Anti-Patterns to Avoid

- Do not blend hat perspectives within a single agent — each must stay strictly in its lane
- Do not let Red Hat justify its feelings — gut reactions only, no analysis
- Do not skip Black Hat to "stay positive" — it is the most commonly omitted and most valuable hat
- Do not let the Chairman reopen analysis — synthesis and decision only, not a sixth round of thinking
- Do not spawn all five hat agents in a single burst — use two batches (3 then 2) to avoid API rate limit caps
- Do not present the Chairman's output and hat outputs at equal visual weight — the recommendation leads

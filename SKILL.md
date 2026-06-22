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

### Step 2 — Stakeholder intake

Before spawning any agents, ask the user:

> "Before we convene the council, who are the key stakeholders in this decision? List each group or individual, and note any known biases, agendas, or stakes they hold. If you're unsure, type 'unknown' and the council will infer from context."

Wait for the user's response. Record it as `{{STAKEHOLDERS}}`. If the user provides nothing useful, note "No stakeholder context provided — council will infer from the problem." Do not proceed to Step 3 until you have a response.

### Step 3 — Spawn all five hat agents in parallel

Spawn all five hat agents at once, running in parallel. Wait for all five to complete before proceeding to Step 4.

**Five agents in parallel:**
- White Hat — facts and information gaps
- Yellow Hat — benefits and optimism
- Black Hat — risks and caution
- Red Hat — stakeholder perspectives and human stakes
- Green Hat — creativity and alternatives

Each call must be fully self-contained — sub-agents have no conversation context. Use the agent prompt templates defined below, substituting `{{PROBLEM}}` with the actual problem statement and `{{STAKEHOLDERS}}` with the stakeholder context from Step 2.

### Step 4 — Spawn the Blue Hat Chairman

After all five agents complete, call the Agent tool once more with the Chairman prompt below, substituting all five hat outputs and the stakeholder context into the template. The chairman synthesizes and decides.

### Step 5 — Present results

Render the result as a React artifact using the reference component in `assets/council-dashboard.jsx`. Do not invent a new layout each run — populate that component so output is consistent across invocations.

Build a single `council` object and pass it to `<CouncilDashboard council={...} />`. The object must be shaped exactly as follows:

```js
council = {
  problem:        string,    // one-line restatement of the decision
  recommendation: string,    // the chairman's RECOMMENDATION, verbatim intent
  convergence:    string,    // the chairman's CONVERGENCE
  tensions:       string[],  // the chairman's KEY TENSIONS, one per item (2–3)
  rationale:      string,    // the chairman's RATIONALE
  nextActions:    string[],  // the chairman's NEXT ACTIONS, ordered (2–3)
  hats: [                    // exactly five, in this order: white, red, yellow, black, green
    {
      key:     "white" | "red" | "yellow" | "black" | "green",
      name:    string,       // e.g. "White Hat"
      role:    string,       // e.g. "Facts"
      summary: string,       // one-sentence distillation of this hat's report
      sections: [            // this hat's labelled sections, verbatim from its output
        { label: string, body: string }
        // a section may instead carry per-group voices in place of a flat body:
        //   { label: string, voices: [{ group: string, body: string }] }
        // Red Hat uses this for OPPOSING VOICES — one entry per opposing group.
      ]
    }
  ]
}
```

Display rules the component already enforces — keep them when adapting it:
- The chairman's verdict leads, in blue, at the top. The recommendation is the single most prominent element on the page.
- The five hat reports sit below as colour-coded cards, **collapsed by default**, so the decision is read first and each perspective is opened on demand.
- The hat colour mapping is fixed by the framework and must not be changed: white = facts, red = stakeholders, yellow = benefits, black = risks, green = creativity. Blue belongs to the chairman only.
- `nextActions` is the only numbered list, because immediate steps are a genuine sequence. The hats are not a sequence — they are identified by colour, never numbered.

If artifacts are unavailable in the current surface, fall back to structured markdown: recommendation first under a clear heading, then convergence, tensions, next actions and rationale, then each hat's full report under its own labelled subheading.

---

## Hat Agent Prompts

These are the exact prompts to use when spawning each hat agent. Substitute `{{PROBLEM}}` with the user's actual problem statement and `{{STAKEHOLDERS}}` with the stakeholder context collected in Step 2. Pass them verbatim — do not summarize or shorten.

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

### Red Hat — Stakeholder Perspectives

You are the Red Hat thinker in a Six Thinking Hats council. Your role is human stakes mapping — and, where stakeholder groups are in genuine conflict, speaking in the emotional voice of each opposing side. For each stakeholder group provided, identify what they stand to gain or lose, what they are likely feeling about this decision, what unstated agendas or biases they bring, and where their interests conflict with others. If no stakeholder context is provided, infer the most likely affected parties from the problem.

Problem: {{PROBLEM}}

Stakeholders: {{STAKEHOLDERS}}

Respond with exactly these sections:

STAKEHOLDER MAP — For each stakeholder group: their likely emotional stance (use specific words: fearful, skeptical, enthusiastic, territorial, indifferent, etc.), what they stand to gain, and what they stand to lose.

HIDDEN AGENDAS — Unstated interests or biases each group is likely to bring to this decision that they may not voice openly.

CONFLICTS OF INTEREST — Where stakeholder interests directly clash with each other or with the stated goal. Name each distinct opposition as its own pairing (e.g. "Group A vs Group B"), so the opposing sides are clearly identified.

OPPOSING VOICES — For each opposition named above, take on the emotional persona of each opposing group in turn and give feedback in that group's own voice: specific to that group, written from inside its stance and feeling, not a neutral third-party summary. Do not merge opposing groups into one blended view — each group speaks for itself, and its feedback must be distinct from the others'. Lead each with the group's name. If no groups are in genuine conflict, write "No opposing groups detected."

WHOSE VOICE IS MISSING — Any affected party not represented in the stakeholder list who should be.

Do not recommend. Do not evaluate the decision itself. Focus entirely on the human landscape around it.

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

Stakeholders: {{STAKEHOLDERS}}

WHITE HAT REPORT (Facts):
{{WHITE_HAT_OUTPUT}}

RED HAT REPORT (Stakeholder Perspectives):
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
- Do not let Red Hat drift into recommending or evaluating the decision — it maps human stakes only
- Do not skip Black Hat to "stay positive" — it is the most commonly omitted and most valuable hat
- Do not let the Chairman reopen analysis — synthesis and decision only, not a sixth round of thinking
- Do not present the Chairman's output and hat outputs at equal visual weight — the recommendation leads, the hats are collapsed supporting detail
- Do not number the five hats or reorder them by importance — they are identified by their fixed framework colours, not by rank
- Do not improvise a fresh layout each run — populate the reference component so output stays consistent
- Do not skip the stakeholder intake step — proceed to agents only after the user has responded

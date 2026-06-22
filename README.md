# Six Thinking Hats — Skill Council

A Claude Code skill that applies Edward de Bono's Six Thinking Hats method as a multi-agent council. Five hat agents analyze a problem in parallel from independent perspectives, then a Blue Hat chairman synthesizes their outputs into a final recommendation.

## How It Works

When invoked, the skill:

1. Extracts the problem from your message
2. Asks you to identify key stakeholders before analysis begins
3. Spawns five hat agents simultaneously via the Agent tool
4. Collects all five reports
5. Passes them to a Blue Hat chairman agent for synthesis
6. Returns the chairman's recommendation as a React artifact, with hat reports collapsed below

The five hats run in parallel — no hat sees another's output before forming its view.

## The Six Hats

| Hat | Role | Output |
|-----|------|--------|
| White | Facts and information gaps | What is known, what is missing, what questions to ask |
| Red | Stakeholder perspectives and human stakes | Emotional stances, hidden agendas, conflicts of interest, opposing voices |
| Yellow | Benefits and optimism | Core benefits, best-case scenario, hidden upsides |
| Black | Risks and caution | Critical risks (ranked), weaknesses, deal-breakers |
| Green | Creativity and alternatives | Alternative approaches, reframes, unconventional ideas |
| Blue | Process and synthesis (Chairman) | Convergence, tensions, recommendation, next actions |

## Usage

Install the skill, then invoke it with any decision or problem:

```
/six-thinking-hats Should we rewrite our authentication service from scratch or refactor the existing one?
```

```
/six-thinking-hats We're considering hiring a third agency to handle social media instead of doing it in-house.
```

```
/six-thinking-hats Our startup has been offered an acquisition at 4x revenue. Should we take it?
```

The skill will pause to ask about stakeholders before convening the council. You can name specific groups (e.g. "engineering team, product manager, enterprise customers") or type `unknown` to let the council infer from context.

The skill works best with decisions that have real stakes, genuine trade-offs, and more than one defensible answer.

## Output Structure

The result renders as a React artifact:

- **Chairman's verdict** — recommendation, convergence, key tensions, rationale, and next actions displayed prominently at the top
- **Hat reports** — five colour-coded cards, collapsed by default, opened on demand

If artifacts are unavailable, the skill falls back to structured markdown with the same ordering: recommendation first, then each hat's full report.

## When to Use

- Decisions where you're tempted to go with your gut without stress-testing it
- Problems where you keep cycling between optimism and anxiety without resolving them
- Situations where you need to present a decision to others and want to show your reasoning
- Any choice where the obvious answer might be wrong

## When Not to Use

- Questions with a factual answer (use search or documentation instead)
- Trivial decisions where the cost of analysis exceeds the cost of a wrong choice
- Situations requiring deep domain expertise the model doesn't have

## Files

- `SKILL.md` — skill definition, orchestration instructions, and all agent prompts
- `assets/council-dashboard.jsx` — React component used to render council output
- `README.md` — this file

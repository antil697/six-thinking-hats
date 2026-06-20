# Six Thinking Hats — Skill Council

A Claude Code skill that applies Edward de Bono's Six Thinking Hats method as a multi-agent council. Five hat agents analyze a problem in parallel from independent perspectives, then a Blue Hat chairman synthesizes their outputs into a final recommendation.

## How It Works

When invoked, the skill:

1. Extracts the problem from your message
2. Spawns five hat agents simultaneously via the Agent tool
3. Collects all five reports
4. Passes them to a Blue Hat chairman agent for synthesis
5. Returns the chairman's recommendation followed by each hat's findings

The five hats run in parallel — no hat sees another's output before forming its view.

## The Six Hats

| Hat | Role | Output |
|-----|------|--------|
| White | Facts and information gaps | What is known, what is missing, what questions to ask |
| Red | Emotions and gut reaction | Instinctive response, feelings, intuitive signals |
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

The skill works best with decisions that have real stakes, genuine trade-offs, and more than one defensible answer.

## Output Structure

```
CHAIRMAN'S RECOMMENDATION
  Convergence
  Key Tensions
  Recommendation        ← clear call, no hedging
  Rationale
  Next Actions

COUNCIL REPORTS
  White Hat — Facts
  Red Hat — Emotions
  Yellow Hat — Benefits
  Black Hat — Risks
  Green Hat — Creativity
```

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
- `README.md` — this file

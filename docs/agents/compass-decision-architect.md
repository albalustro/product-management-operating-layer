# Compass — Product Decision Intelligence Agent

Agent file: [`.claude/agents/compass-decision-architect.md`](../../.claude/agents/compass-decision-architect.md)

## Purpose

Compass tackles the other side of the problem [Radar](radar-signal-synthesizer.md)
solves: once signals have become structured evidence, someone still has to
decide — and it's easy to decide by instinct, by whoever spoke loudest in
the meeting, or by "we can build it, so let's build it."

Compass exists to force rigor at that moment, without taking the decision
out of the hands of whoever is accountable for it. It **does not replace
the human decision-maker** — it structures the problem (objective,
evidence, constraints, options, trade-offs, uncertainty) and delivers an
explicit recommendation with a confidence level and what would change that
recommendation, so the final call is made with informed judgment, not in
the dark.

## How it works

For every task, Compass first frames the problem by identifying:

- the decision to be made;
- the desired outcome;
- the available evidence;
- the relevant constraints;
- the assumptions in play;
- what's missing.

It then identifies reasonable options and evaluates each one on:

- expected value;
- evidence supporting it;
- evidence against it;
- assumptions involved;
- risks;
- reversibility;
- opportunity cost;
- uncertainty.

The output is always a **Decision Brief** with these sections:

1. Decision
2. Context
3. Desired outcome
4. Evidence
5. Options considered
6. Trade-offs
7. Key assumptions
8. Recommendation
9. Confidence level
10. What could change the recommendation
11. Next validation step

Rules followed strictly (they're part of the agent's prompt):

- Subjective judgment is never presented as fact.
- Uncertainty is never hidden — it shows up explicitly in the brief.
- When evidence is weak, the preference is for reversible experiments, not
  big irreversible bets.
- Technical feasibility ("we can build it") is never, on its own, a reason
  to recommend building something.

### Tools and limitations

Like Radar, Compass only has `Read`, `Grep`, and `Glob` — it reads local
files in the repository, it doesn't call APIs, browse the web, or write
files. This keeps the recommendation auditable: everything in the brief
traces back to a file you can open and check, and the agent has no power
to act on the decision itself.

In practice, you need to put the decision's context (objective,
constraints, options, evidence) into text files before requesting an
analysis — see the suggested convention in
[`decisions/README.md`](../../decisions/README.md).

### Relationship with Radar

Compass and Radar form a natural pipeline, although they're independent:

```
raw signals → [Radar] → Product Signal Brief → [Compass] → Decision Brief
   signals/                  signals/briefs/         decisions/briefs/
```

You can point Compass directly at a Product Signal Brief already produced
by Radar (in `signals/briefs/`) as part of a decision's evidence — but
that's not required; Compass also works with any other evidence material
placed in `decisions/`.

## How to configure it

The configuration lives in the front matter of
`.claude/agents/compass-decision-architect.md`:

| Field         | Current value                                  | What it controls |
|---------------|-------------------------------------------------|-------------------|
| `name`        | `compass-decision-architect`                     | Identifier used to invoke the agent explicitly. |
| `description` | description of when to use it                    | Used by Claude Code to decide when to invoke the agent **proactively**. |
| `tools`       | `Read, Grep, Glob`                               | Allowed tools. Keep this restricted to reading — it's what guarantees the agent advises without acting. |
| `model`       | `inherit`                                        | Uses the same model as the main session. Can be pinned (e.g. `claude-opus-5`) for higher-stakes decisions, where extra reasoning power is worth it. |

To adjust the behavior, edit the body of the prompt — for example, to
adapt the Decision Brief's format to your company's internal
prioritization criteria (RICE, ICE, etc.), or to add a check against
specific OKRs. No build step is needed: Claude Code automatically loads
any valid `.md` file inside `.claude/agents/`.

## How to run it in your environment

### Prerequisites

- [Claude Code](https://code.claude.com) installed and authenticated.
- This repository cloned locally (or a Claude Code session on the
  web/GitHub Action pointing at it).

### Step by step

1. Clone and enter the repository (if you haven't already):
   ```bash
   git clone https://github.com/albalustro/product-management-operating-layer.git
   cd product-management-operating-layer
   ```
2. Describe the decision in files inside `decisions/` — objective,
   constraints, options, and available evidence (including, if it exists,
   a Product Signal Brief from Radar). See `decisions/README.md`.
3. Open Claude Code at the root of the repository:
   ```bash
   claude
   ```
   The agent in `.claude/agents/` is discovered automatically when you
   start a session in that folder.
4. Ask for the analysis. Two ways:
   - **Proactive invocation** — describe the decision and let Claude pick
     Compass based on the agent's `description`:
     > "I need to decide whether we invest in self-serve onboarding or a
     > dedicated customer success function next quarter — evaluate the
     > options using what's in decisions/."
   - **Explicit invocation** — ask for it by name when you want to make
     sure it's this specific agent:
     > "Use the compass-decision-architect agent to evaluate the options
     > in decisions/options/onboarding.md against the objective in
     > decisions/context/q3-okrs.md and produce the Decision Brief."
5. The agent returns the Decision Brief as text. Since it doesn't write
   files, saving the brief (e.g. into `decisions/briefs/`) to keep a
   record of the decision is a manual step — done by you or by another
   agent with write permission.

### Running it in CI / as an autonomous agent (optional)

Being read-only, Compass is safe to run in automated environments (Claude
Code on the web, GitHub Actions, scheduled Routines) that revisit
decisions periodically as new evidence lands in `decisions/` or
`signals/`. This isn't set up in this repository — it's a natural
extension, out of scope for the agent itself.

# Radar — Product Signal Intelligence Agent

Agent file: [`.claude/agents/radar-signal-synthesizer.md`](../../.claude/agents/radar-signal-synthesizer.md)

## Purpose

Radar exists to solve a problem every PM runs into: product signals arrive
fragmented — a support ticket here, a stray comment from a sales call
there, an insight buried in a research report somewhere else — and it's
tempting to jump straight to "let's build X" from a single quote or a
handful of repeated complaints.

Radar doesn't do that. It **does not decide what to build, does not
prioritize, and does not invent needs**. Its job is purely synthesis:
take raw evidence (customer feedback, research, support data, analytics,
sales input, market information) and turn it into a structured
**Product Signal Brief** — facts separated from interpretations, patterns
separated from noise, and open questions made explicit — so the product
decision can be made by a human (or by another agent specialized in
prioritization), grounded in traceable evidence.

## How it works

The agent follows a fixed 10-step process every time it analyzes a set of
signals:

1. Identify the sources examined.
2. Extract individual signals.
3. Cluster related signals.
4. Separate facts, observations, interpretations, and assumptions.
5. Identify recurring patterns.
6. Identify contradictory evidence.
7. Identify affected customer segments, when the evidence allows.
8. Estimate the strength of the evidence (how solid it is, not just how
   frequent).
9. Highlight important unanswered questions.
10. Suggest opportunities worth investigating — without recommending
    solutions.

The output is always a **Product Signal Brief** with these sections:

- Executive summary
- Emerging signals
- Repeated problems
- Customer segments affected
- Evidence
- Contradictions
- Possible opportunities
- Unknowns
- Recommended research questions
- Source references

Three rules are followed strictly (they're part of the agent's prompt):

- Frequency alone is never treated as importance.
- A single quote is never treated as a "validated problem."
- No evidence is fabricated — if it's not in the sources, it doesn't go in
  the brief.

### Tools and limitations

The agent only has access to `Read`, `Grep`, and `Glob` — it **reads local
files in the repository**, it doesn't call APIs, browse the web, or write
files. This is intentional: it keeps the agent auditable (everything it
states traces back to a file you can open and check) and without any power
to act.

In practice, this means you need to put the source material into text
files inside the repository before requesting an analysis — see the
suggested convention in [`signals/README.md`](../../signals/README.md).

## How to configure it

The agent's configuration lives entirely in the front matter of
`.claude/agents/radar-signal-synthesizer.md`:

| Field         | Current value                                  | What it controls |
|---------------|-------------------------------------------------|-------------------|
| `name`        | `radar-signal-synthesizer`                       | Identifier used to invoke the agent explicitly. |
| `description` | description of when to use it                    | Used by Claude Code to decide when to invoke the agent **proactively**, without you asking for it by name. |
| `tools`       | `Read, Grep, Glob`                               | Allowed tools. Keep this restricted to reading — it's what guarantees the agent synthesizes without acting. |
| `model`       | `inherit`                                        | Uses the same model as the main session. Can be pinned (e.g. `claude-opus-5`) if you want more synthesis power regardless of cost. |

To adjust the agent's behavior, edit the body of the file (the prompt
itself) — for example, to add an extra analysis step, change the brief's
format, or adapt the vocabulary to your company's internal terms. No
build step or extra registration is needed: Claude Code automatically
loads any valid `.md` file inside `.claude/agents/`.

## How to run it in your environment

### Prerequisites

- [Claude Code](https://code.claude.com) installed (`npm install -g @anthropic-ai/claude-code` or the web/desktop app) and authenticated with your account.
- This repository cloned locally (or a Claude Code session on the web/GitHub Action pointing at it).

### Step by step

1. Clone and enter the repository:
   ```bash
   git clone https://github.com/albalustro/product-management-operating-layer.git
   cd product-management-operating-layer
   ```
2. Put the source material (support exports, research notes, sales call
   transcripts, analytics reports, etc.) inside `signals/`, following the
   convention described in `signals/README.md`.
3. Open Claude Code at the root of the repository:
   ```bash
   claude
   ```
   Since the agent lives in `.claude/agents/`, Claude Code discovers it
   automatically when you start a session in that folder — nothing else
   needs to be installed.
4. Ask for the analysis. Two ways:
   - **Proactive invocation** — just describe the task and let Claude
     decide to use Radar on its own, based on the agent's `description`:
     > "Synthesize the support and sales signals in the signals/ folder
     > about the onboarding problem."
   - **Explicit invocation** — ask for it by name when you want to make
     sure it's this specific agent:
     > "Use the radar-signal-synthesizer agent to analyze
     > signals/support/*.md and signals/sales/*.md and produce the
     > Product Signal Brief."
5. The agent returns the Product Signal Brief as text. Save the result
   wherever makes sense for your PM process (e.g. a file in
   `signals/briefs/` or directly into your team's product management
   tool) — the agent itself doesn't write files, so this step of
   recording/archiving the brief is manual (or done by you/another agent
   with write permission).

### Running it in CI / as an autonomous agent (optional)

Since the agent is read-only, it's safe to run in automated environments
(e.g. Claude Code on the web, GitHub Actions, or a scheduled Routine) that
periodically read a `signals/` folder kept up to date by integrations
(support, CRM, analytics) and produce a recurring brief. This isn't set up
in this repository — it's a natural extension, but out of scope for the
agent itself.

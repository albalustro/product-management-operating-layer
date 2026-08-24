# product-management-operating-layer
An AI-powered Product Management operating layer for turning customer, business, market, and technical signals into better judgment, clearer synthesis, and faster decisions—using reusable PM skills, persistent company context, connected tools, deep research, and decision-ready outputs.

## Agents

Agents in this repository live in [`.claude/agents/`](.claude/agents/) and are
loaded automatically by [Claude Code](https://code.claude.com) when you open
a session in this folder.

| Agent | What it does | Docs |
|---|---|---|
| `radar-signal-synthesizer` | Synthesizes customer feedback, research, support data, analytics, sales input, and market information into a structured Product Signal Brief — without prioritizing or deciding what to build. | [docs/agents/radar-signal-synthesizer.md](docs/agents/radar-signal-synthesizer.md) |
| `compass-decision-architect` | Structures product decisions (prioritization, scope trade-offs, strategic bets) into a Decision Brief with evidence, options, trade-offs, assumptions, and a confidence level — without replacing the human decision-maker. | [docs/agents/compass-decision-architect.md](docs/agents/compass-decision-architect.md) |

Source material for the agents (exports, notes, transcripts) lives in
[`signals/`](signals/); decision context (objectives, constraints, options)
lives in [`decisions/`](decisions/). Together, Radar and Compass form a
pipeline: raw signals → Product Signal Brief → Decision Brief.

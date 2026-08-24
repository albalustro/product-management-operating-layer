# product-management-operating-layer
An AI-powered Product Management operating layer for turning customer, business, market, and technical signals into better judgment, clearer synthesis, and faster decisions—using reusable PM skills, persistent company context, connected tools, deep research, and decision-ready outputs.

## Agentes

Agentes deste repositório vivem em [`.claude/agents/`](.claude/agents/) e são
carregados automaticamente pelo [Claude Code](https://code.claude.com) ao
abrir uma sessão nesta pasta.

| Agente | O que faz | Documentação |
|---|---|---|
| `radar-signal-synthesizer` | Sintetiza feedback de clientes, research, dados de suporte, analytics, input de vendas e informação de mercado em um Product Signal Brief estruturado — sem priorizar ou decidir o que construir. | [docs/agents/radar-signal-synthesizer.md](docs/agents/radar-signal-synthesizer.md) |
| `compass-decision-architect` | Estrutura decisões de produto (priorização, trade-offs de escopo, apostas estratégicas) em um Decision Brief com evidência, opções, trade-offs, suposições e nível de confiança — sem substituir o decisor humano. | [docs/agents/compass-decision-architect.md](docs/agents/compass-decision-architect.md) |

Material de origem para os agentes (exports, notas, transcrições) fica em
[`signals/`](signals/); contexto de decisões (objetivos, restrições,
opções) fica em [`decisions/`](decisions/). Juntos, Radar e Compass formam
um pipeline: sinais brutos → Product Signal Brief → Decision Brief.

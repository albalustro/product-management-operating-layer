# Compass — Product Decision Intelligence Agent

Arquivo do agente: [`.claude/agents/compass-decision-architect.md`](../../.claude/agents/compass-decision-architect.md)

## Para que serve

Compass ataca o outro lado do problema que o [Radar](radar-signal-synthesizer.md)
resolve: depois que os sinais viraram evidência estruturada, alguém ainda
precisa decidir — e é fácil decidir por instinto, por quem falou mais alto
na reunião, ou por "dá pra construir, então vamos construir".

Compass existe para forçar rigor nesse momento, sem tirar a decisão das
mãos de quem é responsável por ela. Ele **não substitui o decisor humano**
— ele estrutura o problema (objetivo, evidência, restrições, opções,
trade-offs, incerteza) e entrega uma recomendação explícita com o nível de
confiança e o que faria essa recomendação mudar, para que a decisão final
seja tomada com judgment informado, não no escuro.

## Como funciona

Para cada tarefa, Compass primeiro delimita o problema, identificando:

- a decisão a ser tomada;
- o resultado desejado;
- a evidência disponível;
- as restrições relevantes;
- as suposições em jogo;
- o que está faltando saber.

Em seguida, identifica opções razoáveis e avalia cada uma em:

- valor esperado;
- evidência a favor;
- evidência contra;
- suposições envolvidas;
- riscos;
- reversibilidade;
- custo de oportunidade;
- incerteza.

A saída é sempre um **Decision Brief** com estas seções:

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

Regras seguidas à risca (fazem parte do prompt do agente):

- Julgamento subjetivo nunca é apresentado como fato.
- Incerteza nunca é escondida — ela aparece explicitamente no brief.
- Quando a evidência é fraca, a preferência é por experimentos reversíveis,
  não por apostas grandes e irreversíveis.
- Viabilidade técnica ("dá pra construir") nunca é, sozinha, motivo para
  recomendar construir algo.

### Ferramentas e limitações

Assim como o Radar, Compass só tem `Read`, `Grep` e `Glob` — lê arquivos
locais do repositório, não acessa APIs, não navega na web e não escreve
arquivos. Isso mantém a recomendação auditável: tudo que aparece no brief
remete a um arquivo que você pode abrir e conferir, e o agente não tem poder
de agir sobre a decisão sozinho.

Na prática, você precisa colocar o contexto da decisão (objetivo,
restrições, opções, evidência) em arquivos texto antes de pedir a análise —
veja a convenção sugerida em [`decisions/README.md`](../../decisions/README.md).

### Relação com o Radar

Compass e Radar formam um pipeline natural, embora sejam independentes:

```
sinais brutos → [Radar] → Product Signal Brief → [Compass] → Decision Brief
   signals/                  signals/briefs/         decisions/briefs/
```

Você pode apontar o Compass diretamente para um Product Signal Brief já
gerado pelo Radar (em `signals/briefs/`) como parte da evidência de uma
decisão — mas isso não é obrigatório; o Compass também funciona com
qualquer outro material de evidência colocado em `decisions/`.

## Como configurar

A configuração vive no front matter de
`.claude/agents/compass-decision-architect.md`:

| Campo         | Valor atual                                  | O que controla |
|---------------|-----------------------------------------------|----------------|
| `name`        | `compass-decision-architect`                  | Identificador usado para invocar o agente explicitamente. |
| `description` | descrição de quando usar                      | Usado pelo Claude Code para decidir quando invocar o agente **proativamente**. |
| `tools`       | `Read, Grep, Glob`                            | Ferramentas permitidas. Mantenha restrito a leitura — é o que garante que o agente aconselhe, sem agir. |
| `model`       | `inherit`                                     | Usa o mesmo modelo da sessão principal. Pode ser fixado (ex. `claude-opus-5`) para decisões de maior peso, onde vale a pena mais poder de raciocínio. |

Para ajustar o comportamento, edite o corpo do prompt — por exemplo, para
adaptar o formato do Decision Brief aos critérios de priorização internos
da empresa (RICE, ICE, etc.), ou para adicionar um passo de checagem contra
OKRs específicos. Nenhum passo de build é necessário: o Claude Code carrega
qualquer `.md` válido em `.claude/agents/` automaticamente.

## Como rodar no seu ambiente

### Pré-requisitos

- [Claude Code](https://code.claude.com) instalado e autenticado.
- Este repositório clonado localmente (ou uma sessão do Claude Code na
  web/GitHub Action apontando para ele).

### Passo a passo

1. Clone e entre no repositório (se ainda não tiver feito):
   ```bash
   git clone https://github.com/albalustro/product-management-operating-layer.git
   cd product-management-operating-layer
   ```
2. Descreva a decisão em arquivos dentro de `decisions/` — objetivo,
   restrições, opções e evidência disponível (incluindo, se existir, um
   Product Signal Brief do Radar). Veja `decisions/README.md`.
3. Abra o Claude Code na raiz do repositório:
   ```bash
   claude
   ```
   O agente em `.claude/agents/` é descoberto automaticamente ao iniciar a
   sessão nessa pasta.
4. Peça a análise. Duas formas:
   - **Invocação proativa** — descreva a decisão e deixe o Claude escolher o
     Compass com base na `description` do agente:
     > "Preciso decidir se investimos em onboarding self-serve ou em
     > customer success dedicado no próximo trimestre — avalie as opções
     > usando o que está em decisions/."
   - **Invocação explícita** — peça pelo nome quando quiser garantir que é
     este agente específico:
     > "Use o agente compass-decision-architect para avaliar as opções em
     > decisions/options/onboarding.md contra o objetivo em
     > decisions/context/q3-okrs.md e gerar o Decision Brief."
5. O agente devolve o Decision Brief em texto. Como ele não escreve
   arquivos, salvar o brief (ex. em `decisions/briefs/`) para manter
   histórico da decisão é um passo manual — feito por você ou por outro
   agente com permissão de escrita.

### Rodando em CI / agente autônomo (opcional)

Por ser somente leitura, o Compass é seguro para rodar em ambientes
automatizados (Claude Code on the web, GitHub Actions, Routines agendadas)
que revisitem decisões periodicamente à medida que nova evidência chega em
`decisions/` ou `signals/`. Isso não está configurado neste repositório —
é uma extensão natural, fora do escopo do agente em si.

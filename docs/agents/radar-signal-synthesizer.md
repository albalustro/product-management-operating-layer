# Radar — Product Signal Intelligence Agent

Arquivo do agente: [`.claude/agents/radar-signal-synthesizer.md`](../../.claude/agents/radar-signal-synthesizer.md)

## Para que serve

Radar existe para resolver um problema comum de PM: sinais de produto chegam
fragmentados — um ticket de suporte aqui, uma frase solta de uma call de
vendas ali, um insight de pesquisa em outro lugar — e é tentador pular direto
para "vamos construir X" a partir de uma única citação ou de um punhado de
reclamações repetidas.

Radar não faz isso. Ele **não decide o que construir, não prioriza e não
inventa necessidades**. O trabalho dele é puramente de síntese: pegar
evidências brutas (feedback de clientes, pesquisa, dados de suporte,
analytics, input de vendas, informação de mercado) e transformá-las em um
**Product Signal Brief** estruturado — fatos separados de interpretações,
padrões separados de ruído, e perguntas em aberto explicitadas — para que a
decisão de produto seja tomada por um humano (ou por outro agente
especializado em priorização), com base em evidência rastreável.

## Como funciona

O agente segue um processo fixo de 10 passos sempre que analisa um conjunto
de sinais:

1. Identifica as fontes examinadas.
2. Extrai sinais individuais.
3. Agrupa sinais relacionados (clustering).
4. Separa fatos, observações, interpretações e suposições.
5. Identifica padrões recorrentes.
6. Identifica evidências contraditórias.
7. Identifica segmentos de clientes afetados, quando a evidência permite.
8. Estima a força da evidência (quão sólida é, não apenas quão frequente).
9. Destaca perguntas importantes que ainda não têm resposta.
10. Sugere oportunidades a investigar — sem recomendar soluções.

A saída é sempre um **Product Signal Brief** com estas seções:

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

Três regras seguidas à risca (fazem parte do prompt do agente):

- Frequência sozinha nunca vira sinônimo de importância.
- Uma única citação nunca vira "problema validado".
- Nenhuma evidência é fabricada — se não está nas fontes, não entra no brief.

### Ferramentas e limitações

O agente só tem acesso a `Read`, `Grep` e `Glob` — ou seja, ele **lê arquivos
locais do repositório**, não acessa APIs, não navega na web e não escreve
arquivos. Isso é proposital: mantém o agente auditável (tudo que ele afirma
vem de um arquivo que você pode abrir e conferir) e sem poder de execução.

Na prática, isso significa que você precisa colocar o material de origem em
arquivos texto dentro do repositório antes de pedir a análise — veja a
convenção sugerida em [`signals/README.md`](../../signals/README.md).

## Como configurar

A configuração do agente vive inteiramente no front matter do arquivo
`.claude/agents/radar-signal-synthesizer.md`:

| Campo         | Valor atual                                  | O que controla |
|---------------|-----------------------------------------------|----------------|
| `name`        | `radar-signal-synthesizer`                    | Identificador usado para invocar o agente explicitamente. |
| `description` | descrição de quando usar                      | Usado pelo Claude Code para decidir quando invocar o agente **proativamente**, sem você pedir por nome. |
| `tools`       | `Read, Grep, Glob`                            | Ferramentas permitidas. Mantenha restrito a leitura — é o que garante que o agente não "aja", só sintetize. |
| `model`       | `inherit`                                     | Usa o mesmo modelo da sessão principal. Pode ser fixado (ex. `claude-opus-5`) se quiser mais poder de síntese custe o que custar. |

Para ajustar o comportamento do agente, edite o corpo do arquivo (o prompt
em si) — por exemplo, para adicionar um passo extra de análise, mudar o
formato do brief, ou adaptar o vocabulário aos termos internos da sua
empresa. Não é necessário nenhum passo de "build" ou registro adicional: o
Claude Code carrega qualquer arquivo `.md` válido dentro de `.claude/agents/`
automaticamente.

## Como rodar no seu ambiente

### Pré-requisitos

- [Claude Code](https://code.claude.com) instalado (`npm install -g @anthropic-ai/claude-code` ou a app web/desktop) e autenticado na sua conta.
- Este repositório clonado localmente (ou uma sessão do Claude Code na web/GitHub Action apontando para ele).

### Passo a passo

1. Clone e entre no repositório:
   ```bash
   git clone https://github.com/albalustro/product-management-operating-layer.git
   cd product-management-operating-layer
   ```
2. Coloque o material de origem (exports de suporte, notas de research,
   transcrições de vendas, relatórios de analytics, etc.) dentro de
   `signals/`, seguindo a convenção descrita em `signals/README.md`.
3. Abra o Claude Code na raiz do repositório:
   ```bash
   claude
   ```
   Como o agente está em `.claude/agents/`, o Claude Code o descobre
   automaticamente ao iniciar a sessão nessa pasta — não é preciso instalar
   nada além disso.
4. Peça a análise. Duas formas:
   - **Invocação proativa** — basta descrever a tarefa e o Claude decide usar
     o Radar sozinho, com base na `description` do agente:
     > "Sintetize os sinais de suporte e vendas da pasta signals/ sobre o
     > problema de onboarding."
   - **Invocação explícita** — peça pelo nome quando quiser garantir que é
     este agente específico:
     > "Use o agente radar-signal-synthesizer para analisar
     > signals/support/*.md e signals/sales/*.md e gerar o Product Signal
     > Brief."
5. O agente devolve o Product Signal Brief em texto. Salve o resultado onde
   fizer sentido para o seu processo de PM (ex. um arquivo em
   `signals/briefs/` ou direto na ferramenta de gestão de produto usada pelo
   time) — o próprio agente não escreve arquivos, então esse passo de
   registrar/arquivar o brief é manual (ou feito por você/outro agente com
   permissão de escrita).

### Rodando em CI / agente autônomo (opcional)

Como o agente é só leitura, ele é seguro para rodar em ambientes
automatizados (ex. Claude Code on the web, GitHub Actions, ou uma Routine
agendada) que leiam periodicamente uma pasta `signals/` atualizada por
integrações (suporte, CRM, analytics) e produzam um brief recorrente. Isso
não está configurado neste repositório — é uma extensão natural, mas fica
fora do escopo do agente em si.

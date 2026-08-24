# decisions/

Ponto de entrada de contexto para agentes de apoio à decisão (como o
**Compass**). Assim como o Radar em `signals/`, o Compass só tem ferramentas
de leitura (`Read`, `Grep`, `Glob`) — ele não busca contexto sozinho, então
o material precisa estar em arquivos texto dentro do repositório antes de
pedir a análise.

## Estrutura sugerida

```
decisions/
  briefs/         # Decision Briefs já produzidos pelo Compass (histórico)
  context/        # objetivos, OKRs, constraints, prazos, orçamento por decisão
  options/        # notas sobre as opções/alternativas em avaliação
```

Nenhuma subpasta é obrigatória. O importante é que, para cada decisão que
você quiser levar ao Compass, existam arquivos descrevendo:

- **a decisão em si** (o que precisa ser decidido, até quando);
- **o objetivo desejado** (que resultado essa decisão deve produzir);
- **restrições** (orçamento, prazo, capacidade de engenharia, compromissos já
  assumidos);
- **evidência disponível** — inclusive, quando existir, um **Product Signal
  Brief** gerado pelo [Radar](../signals/README.md) em `signals/briefs/`,
  que serve como insumo direto de evidência para o Compass.

## Dados sensíveis

O mesmo cuidado de `signals/` se aplica aqui: se os arquivos contiverem
informação estratégica sensível (números de orçamento, roadmap não
anunciado), avalie manter fora do controle de versão via `.gitignore` e
tratar este diretório como workspace local.

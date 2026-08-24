# signals/

Esta pasta é o ponto de entrada de evidências para os agentes de sinal deste
repositório (como o **Radar**). Como esses agentes só têm ferramentas de
leitura (`Read`, `Grep`, `Glob`), eles não buscam dados sozinhos em
sistemas externos — você precisa colocar (ou exportar) o material aqui antes
de pedir a análise.

## Estrutura sugerida

```
signals/
  support/        # exports de tickets de suporte, transcrições de chat
  sales/          # notas de calls de vendas, objeções, perdas de deal
  research/       # entrevistas de usuário, testes de usabilidade, surveys
  analytics/      # relatórios/exports de produto (funis, retenção, uso)
  market/         # análises de concorrência, reviews públicos, analistas
  misc/           # qualquer outro sinal que não se encaixe acima
```

Nenhuma dessas subpastas é obrigatória — crie apenas o que fizer sentido
para o sinal que você está analisando. Cada arquivo deve ser texto legível
(`.md`, `.txt`, `.csv`, `.json`) para que o agente consiga ler e cruzar as
fontes.

## Dados sensíveis

Se os arquivos aqui contiverem dados pessoais de clientes (nomes, e-mails,
transcrições), avalie anonimizar antes de commitar, ou mantenha a pasta (ou
subpastas específicas) fora do controle de versão via `.gitignore` e trate
este diretório como um workspace local.

# Blueprint: MGMT — Parceria Taqtile

## Tese Central

> A MGMT tem produto vivo e receita — o próximo passo é evoluir sem medo de quebrar o que já funciona. A Taqtile entra como parceira sênior para montar com você uma rede de evals e acompanhar a reescrita do LangChain como migração controlada. Ao final, você opera tudo com autonomia — a Taqtile segue por perto, sem virar dependência.

## Visão Geral

Engajamento de parceria técnica em três fases. Substitui a ideia de AI Sprint (inadequada para produto vivo) e evita alocação genérica (que commoditiza o valor). Escopo cirúrgico, hand-off inegociável, cadência part-time para preservar autonomia da MGMT.

## Atores

| Ator | Cor | Papel |
|------|-----|-------|
| MGMT | #1565C0 | Cliente. Produto vivo com receita. Ponto único técnico e operacional. |
| Taqtile | #FF7A0D | Parceiro técnico sênior. Metodologia, plataforma interna de validação, especialização em IA conversacional regulada. |

## Fases

| Fase | Nome | Duração | Steps |
|------|------|---------|-------|
| 1 | Imersão e Framework de Evals | 4-6 semanas | 10 |
| 2 | Apoio à Reescrita LangChain | 8-12 semanas | 9 |
| 3 | Hand-off e Autonomia | 2-3 semanas | 5 |

## Fase 1 — Imersão e Framework de Evals

Objetivo: trancar a rede de proteção antes de mexer no motor. Sai com suíte de evals rodando em CI, métricas estáveis e MGMT treinada na operação básica.

Steps: Kick-off → Imersão no produto → Análise de logs → Definição de cenários críticos (wait) → Desenho do framework → Implementação da suíte base → Integração CI/CD (wait) → Validação com cenários reais → Bateria semanal inicial → Calibração de thresholds (wait).

## Fase 2 — Apoio à Reescrita LangChain

Objetivo: migrar LangChain 0.14 → 1.10 com a suíte da Fase 1 como safety net. Ataca a dor crônica de memória/determinismo. Rollout controlado antes do rollout completo.

Steps: Planejamento da migração → Mapeamento de dependências → Prototipagem LangChain 1.10 → Migração cadeia-a-cadeia → Testes de regressão contínuos → Tratamento de drift e memória → Rollout controlado (wait) → Rollout completo → Estabilização pós-rollout (wait).

## Fase 3 — Hand-off e Autonomia

Objetivo: MGMT operando o framework sozinha. Documentação acionável, treinamento prático, runbooks para cenários críticos, cadência opcional de check-in pós-saída.

Steps: Documentação operacional → Treinamento operacional → Runbook de situações críticas → Call de transição final → Cadência opcional de check-in.

## Sistemas mapeados

- Amazon Bedrock (restrição regulatória MGMT)
- LangChain 0.14 (atual) → LangChain 1.10 (alvo)
- Python
- CI/CD MGMT
- Plataforma de validação conversacional da Taqtile
- LangWatch Scenario (observabilidade)
- Plataforma MGMT (produto em operação)
- Google Meet / Notion / Miro (ferramentas de colaboração)

## Níveis de IA aplicados ao engajamento

- **Assistente** — IA resume código, clusteriza erros por similaridade, sugere cenários de teste, compara APIs 0.14 vs 1.10, sinaliza divergências ground truth.
- **Orquestração** — IA executa baterias de 10+20+100 iterações, gera relatórios de validação ranqueados, prototipa variações arquiteturais, orquestra rollout gradual (5% → 100%).
- **Automação** — Pipeline executa bateria em cada PR, merge bloqueado se eval falha, rollback automático em regressão, gate de progressão baseado em métrica, relatório mensal de saúde pós-hand-off.

## Pontos de atenção transversais

- MGMT como ponto único técnico: qualquer indisponibilidade vira gargalo para Taqtile → cadência part-time (2-3 dias/semana) protege contra ociosidade custosa.
- Compliance Bedrock é restrição dura: toda decisão arquitetural precisa considerar o perímetro regulatório.
- Custo de execução da suíte (~$15-20/sem durante desenvolvimento) precisa caber no orçamento MGMT.
- Hand-off é inegociável: runbook, treinamento prático e check-in opcional evitam que a Taqtile vire staff aug disfarçado.

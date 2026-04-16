# Blueprint: PipeLovers — Suporte WhatsApp com Agente Nível-0

## Visão Geral

Mapeamento operacional do atendimento atual da PipeLovers aos 5.000+ alunos via WhatsApp, com foco central na implementação de um Agente Nível-0 de IA capaz de absorver autonomamente os 90% de dúvidas de acesso/login e a maior parte dos 8% de dúvidas financeiras com respostas padrão. O blueprint explicita os 3 níveis possíveis de IA (Assistente, Orquestração, Automação) em cada step, permitindo que Gabriel e o time identifiquem onde faz sentido começar e onde manter decisão humana (negociação financeira complexa, resolução de bugs técnicos). O cenário atual é marcado por triagem informal, handoffs com perda de contexto, operação manual em tarefas repetitivas (reset de senha, 2ª via de boleto) e ausência de loop de aprendizado entre atendimento e produto.

## Definições

### Fases

| Fase | Nome | Steps |
|------|------|-------|
| 1 | Chegada e Triagem | 4 |
| 2 | Resolução Nível-0 — Acesso/Login (90%) | 6 |
| 3 | Rota Financeira (8%) | 6 |
| 4 | Escalonamento Técnico (2%) | 5 |
| 5 | Pós-atendimento e Aprendizado | 5 |

### Atores

| Ator | Cor | Papel |
|------|-----|-------|
| Aluno | #37474F | Cliente final, 5.000+ ativos na plataforma |
| Agente Nível-0 IA | #7B1FA2 | Novo agente de IA no WhatsApp (foco do projeto) |
| Atendente Nível-1 | #2E7D32 | Humano de suporte que recebe escaladas do nível-0 |
| Financeiro | #5D4037 | Time financeiro para negociação e inadimplência |
| Tech | #4527A0 | Desenvolvimento / resolução de bugs |
| Supervisor Suporte | #1565C0 | Gestão de qualidade e escala do time |
| Head of Revenue | #E65100 | Gabriel — sponsor e consumidor de reporting |

### Sistemas

- **WhatsApp Business** — canal único de contato com o aluno
- **LMS (Plataforma de Ensino)** — onde o aluno consome as aulas e onde nível-0 faz reset de senha
- **Gateway Pagamento** — emissão de 2ª via, status de pagamento
- **ERP** — sistema financeiro para acordos e negociações
- **CRM** — ficha do aluno, histórico de tickets
- **Base de Conhecimento** — artigos e tutoriais
- **LLM (Anthropic, Gemini)** — já em uso interno para geração de PDIs
- **Ticketing** — abertura de tickets técnicos com SLA
- **Dashboards** — reporting executivo

---

## Fase 1: Chegada e Triagem

### 1.1 Aluno inicia contato no WhatsApp

**Atores:** Aluno
**Wait time:** não
**Humor:** 2

**Processo atual:**
Aluno encontra o número da PipeLovers (site, rodapé de email, materiais do curso) e envia mensagem descrevendo o problema — "não consigo entrar", "esqueci a senha", "meu boleto". Não há confirmação automática do recebimento fora do horário comercial.

**Dores:**
- Mensagens fora do horário comercial ficam sem resposta até o dia seguinte
- Aluno não sabe se mensagem foi vista ou qual a fila
- Alunos enviam mesma dúvida por múltiplos canais por falta de retorno

**Sistemas:** WhatsApp Business

**Artefatos:**
- Mensagem inicial do aluno

**Tempo:** instantâneo | Espera: 0-24h (fora de horário)

**Assistente:** —
**Orquestração:** —
**Automação:** Resposta automática de boas-vindas com ETA de resposta humana + coleta inicial estruturada

**Métricas:** Volume de mensagens/dia, % fora de horário, mensagens duplicadas por aluno
**Riscos:** Expectativa de resposta humana imediata gera frustração

---

### 1.2 Identificação do aluno

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente pede CPF/matrícula, busca manualmente em LMS e CRM para puxar ficha. Leva 2-5 minutos e depende da consistência dos cadastros.

**Dores:**
- Identificação manual, risco de trocar aluno em atendimentos paralelos
- Aluno precisa repetir dados a cada novo contato
- Credenciais de atendentes nos sistemas muitas vezes desatualizadas

**Sistemas:** WhatsApp Business, LMS, CRM

**Artefatos:**
- Ficha do aluno identificada (curso, status, histórico de tickets)

**Tempo:** 2-5min | Espera: 0

**Assistente:** IA sugere match do aluno pelo número de telefone cadastrado
**Orquestração:** IA consolida perfil do aluno (curso, pagamentos, tickets anteriores, nível de engajamento) em snapshot estruturado
**Automação:** Identificação instantânea por telefone + carregamento automático do contexto completo, sem intervenção

**Métricas:** Taxa de match automático, tempo médio até identificação
**Riscos:** Número divergente do cadastro; exposição de dados sensíveis (LGPD)

---

### 1.3 Classificação de intenção

**Atores:** Agente Nível-0 IA
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente lê a mensagem e classifica mentalmente em acesso/financeiro/tech. Classificação é inconsistente entre atendentes, sem métrica objetiva do mix de demanda.

**Dores:**
- Critério varia por atendente
- Fila única sem priorização real
- Sem visibilidade do mix 90/8/2 em tempo real

**Sistemas:** WhatsApp Business, LLM

**Artefatos:**
- Intenção classificada (acesso / financeiro / tech) + subcategoria + score de confiança

**Tempo:** 30s-2min | Espera: 0

**Assistente:** IA sugere categoria ao atendente humano durante triagem
**Orquestração:** IA classifica e propõe rota; humano valida apenas casos ambíguos ou com score baixo
**Automação:** Classificação autônoma com confidence score; rota direta quando score > threshold

**Métricas:** Acurácia de classificação, % de ambiguidades, distribuição real 90/8/2
**Riscos:** Má classificação envia caso crítico para fluxo errado

---

### 1.4 Roteamento

**Atores:** Agente Nível-0 IA, Supervisor Suporte
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente decide a próxima ação ou repassa internamente via grupo de Slack/WhatsApp. Handoff informal, contexto se perde na transferência.

**Dores:**
- Handoff manual por mensagem em grupo
- Aluno transferido precisa repetir histórico
- Sem ownership claro do atendimento

**Sistemas:** WhatsApp Business, Ticketing

**Artefatos:**
- Rota definida + ticket estruturado (quando aplicável)

**Tempo:** 1-3min | Espera: 0

**Assistente:** IA sugere fila e prioridade ao supervisor
**Orquestração:** IA cria ticket estruturado com contexto completo e roteia para fila apropriada
**Automação:** Roteamento autônomo — 90% dos casos entram direto no fluxo nível-0 sem humano

**Métricas:** % roteadas corretamente, tempo de roteamento, taxa de re-roteamento
**Riscos:** Loop entre filas; perda de ownership entre nível-0 e nível-1

---

## Fase 2: Resolução Nível-0 — Acesso/Login (90%)

### 2.1 Diagnóstico de acesso

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente pergunta "qual erro aparece?", "já tentou X?", "de qual dispositivo?". Diagnóstico por tentativa, com muita troca de mensagens.

**Dores:**
- Aluno não consegue descrever erro técnico
- Fotos borradas, screenshots cortados
- Idas e voltas longas antes de entender o problema

**Sistemas:** WhatsApp Business, LLM

**Artefatos:**
- Diagnóstico categorizado (senha esquecida, email errado, conta bloqueada, app desatualizado, etc.)

**Tempo:** 3-8min | Espera: 0

**Assistente:** IA sugere perguntas de diagnóstico ao atendente humano
**Orquestração:** IA conduz diagnóstico via perguntas estruturadas, chega ao root cause e propõe solução
**Automação:** Diagnóstico 100% IA com árvore de decisão adaptativa, resolução em < 1 minuto nos casos clássicos

**Métricas:** Acurácia de diagnóstico, nº de trocas até diagnóstico, taxa de desistência do aluno durante diagnóstico
**Riscos:** Aluno desiste se o fluxo for longo demais

---

### 2.2 Recuperação de senha / reset

**Atores:** Agente Nível-0 IA
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente abre o admin do LMS, localiza conta, aciona reset e reenvia link por WhatsApp. Tarefa mecânica, altamente repetitiva.

**Dores:**
- Operação repetitiva consome atendente experiente
- Risco de reset em conta errada (contas com mesmo nome)
- Credenciais admin compartilhadas entre atendentes

**Sistemas:** LMS (admin), WhatsApp Business

**Artefatos:**
- Link de reset enviado + confirmação de recebimento

**Tempo:** 2-4min | Espera: 0

**Assistente:** IA prepara o payload do reset para humano aprovar com um clique
**Orquestração:** IA aciona o reset no LMS e envia link via WhatsApp; humano acompanha via dashboard
**Automação:** Reset ponta-a-ponta sem humano, com guardrails (validação por últimos dígitos do telefone cadastrado)

**Métricas:** Volume de resets/dia, taxa de sucesso no primeiro envio, % automatizados
**Riscos:** Reset em conta errada; fraude (terceiro pedindo reset)

---

### 2.3 Orientação de acesso

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente envia tutorial por texto ou screenshot. Respostas variam entre atendentes; tutoriais da base de conhecimento nem sempre são usados.

**Dores:**
- Copy-paste entre atendentes
- Inconsistência nas explicações
- Tutorial da base defasado em relação à UI atual

**Sistemas:** WhatsApp Business, Base de Conhecimento, LLM

**Artefatos:**
- Tutorial personalizado (web / app iOS / Android), com mídia quando necessário

**Tempo:** 2-5min | Espera: 0

**Assistente:** IA sugere artigo da base para o atendente anexar
**Orquestração:** IA gera resposta contextualizada (curso, dispositivo, navegador) a partir da base, humano aprova
**Automação:** Resposta instantânea e personalizada, com mídia (GIFs/prints da base) e follow-up

**Métricas:** Taxa de resolução no primeiro envio, nº de interações até resolução
**Riscos:** Tutorial desatualizado gera nova dúvida

---

### 2.4 Validação da resolução

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente pergunta "conseguiu?" e aguarda confirmação. Muitos alunos não retornam, ticket fica em estado indefinido.

**Dores:**
- Silent dropout — aluno sai sem confirmar
- Tickets zumbis inflam métrica de atendimento aberto
- Sem visibilidade se problema realmente foi resolvido

**Sistemas:** WhatsApp Business

**Artefatos:**
- Confirmação de resolução ou reabertura do atendimento

**Tempo:** 1-2min | Espera: 5min-2h (aluno testar)

**Assistente:** IA redige a pergunta de follow-up ao atendente enviar
**Orquestração:** IA envia follow-up programado e reabre atendimento se aluno responder negativamente
**Automação:** Follow-up automático em cadência (5min, 1h, 24h); fecha ticket no silêncio e dispara NPS

**Métricas:** Taxa de confirmação explícita, % de tickets zumbis, reabertura pós-fechamento
**Riscos:** Fechar cedo demais quando aluno ainda está tentando

---

### 2.5 Escalada para nível-1 (quando nível-0 falha)

**Atores:** Agente Nível-0 IA, Atendente Nível-1
**Wait time:** sim
**Humor:** 2

**Processo atual:**
Problema não resolve no nível-0 e é repassado para nível-1 com contexto parcial via mensagem em grupo. Aluno frequentemente precisa repetir histórico.

**Dores:**
- Handoff com contexto incompleto
- Aluno repete o histórico do zero
- Tempo de espera elevado na fila do nível-1

**Sistemas:** WhatsApp Business, Ticketing, CRM

**Artefatos:**
- Ticket escalado com histórico completo da conversa + diagnóstico tentado

**Tempo:** 2-3min | Espera: 10min-2h

**Assistente:** IA sugere notas de escalada para humano revisar antes de passar
**Orquestração:** IA gera resumo estruturado + próximos passos sugeridos para o nível-1
**Automação:** Escalada automática com threshold (3 tentativas ou confidence baixa); histórico completo já chega no nível-1

**Métricas:** Taxa de escalada indevida (poderia ter resolvido no 0), tempo de espera no nível-1
**Riscos:** Subir casos simples demais (sobrecarga) ou reter casos complexos (frustração)

---

### 2.6 Registro e fechamento

**Atores:** Agente Nível-0 IA
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente registra resolução em planilha ou CRM. Muitos tickets sem registro completo — tags inconsistentes.

**Dores:**
- Dados inconsistentes entre atendentes
- FCR (First Contact Resolution) impossível de medir com confiança
- Categorização varia semana a semana

**Sistemas:** CRM, Ticketing, Base de Conhecimento

**Artefatos:**
- Ticket fechado + tag de categoria + resumo da resolução

**Tempo:** 1-2min | Espera: 0

**Assistente:** IA sugere tags ao atendente no fechamento
**Orquestração:** IA gera resumo estruturado e atualiza CRM após revisão humana
**Automação:** Registro automático ponta-a-ponta, incluindo enriquecimento da base quando detecta padrão novo

**Métricas:** % tickets com registro completo, FCR, distribuição real de categorias
**Riscos:** Dados ruins = decisões ruins sobre dimensionamento do time

---

## Fase 3: Rota Financeira (8%)

### 3.1 Identificação da dúvida financeira

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente identifica o subtipo: 2ª via de boleto, status de pagamento, troca de cartão, cancelamento, inadimplência em negociação. Cada subtipo exige rota diferente.

**Dores:**
- Alta variedade de subtipos, fácil confundir
- Sem separação clara entre "resposta padrão" e "negociação"
- Dúvida simples demora tanto quanto disputa complexa

**Sistemas:** WhatsApp Business, LLM

**Artefatos:**
- Subtipo da demanda financeira identificado

**Tempo:** 2-3min | Espera: 0

**Assistente:** IA sugere subcategoria ao atendente
**Orquestração:** IA classifica subtipo e já carrega dados do aluno no ERP/gateway
**Automação:** Classificação + carregamento instantâneo de dados financeiros; rota padrão vs. escalada decidida autonomamente

**Métricas:** Distribuição por subtipo, acurácia da classificação
**Riscos:** Confundir dúvida simples com reclamação crítica (inadimplência em disputa)

---

### 3.2 Consulta a sistemas financeiros

**Atores:** Agente Nível-0 IA
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente abre gateway, ERP e LMS, busca por CPF, consulta status do pagamento. Multi-sistema, lento, credenciais dispersas.

**Dores:**
- Troca de abas e credenciais
- Dados dispersos entre gateway e ERP
- Tempo alto para uma consulta de 30 segundos

**Sistemas:** Gateway Pagamento, ERP, CRM

**Artefatos:**
- Snapshot financeiro do aluno (fatura atual, vencimento, status, histórico)

**Tempo:** 3-6min | Espera: 0

**Assistente:** IA consulta e resume os dados para o atendente
**Orquestração:** IA busca em múltiplos sistemas e consolida resposta estruturada
**Automação:** Integração direta — IA lê gateway/ERP e devolve resposta ao aluno sem humano

**Métricas:** Tempo de consulta, completude da resposta, taxa de erro em dados
**Riscos:** Dado desatualizado; conciliação divergente entre ERP e gateway

---

### 3.3 Resposta padrão (2ª via, status, comprovante)

**Atores:** Agente Nível-0 IA
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente envia 2ª via de boleto, PIX copia-cola ou comprovante. Tarefa puramente operacional — consome atendente experiente.

**Dores:**
- Atividade mecânica de copy/paste
- Consome recurso experiente em operação básica
- Erro de envio para pessoa errada tem impacto

**Sistemas:** Gateway Pagamento, WhatsApp Business

**Artefatos:**
- Boleto / PIX / comprovante enviado ao aluno

**Tempo:** 2-4min | Espera: 0

**Assistente:** IA prepara o arquivo e o atendente só aprova o envio
**Orquestração:** IA gera e envia arquivo; humano acompanha a fila
**Automação:** IA emite 2ª via, envia PIX copia-cola, devolve comprovante — resposta < 30s

**Métricas:** Volume de 2ª vias/dia, % automatizadas, taxa de entrega correta
**Riscos:** Enviar boleto/PIX à pessoa errada

---

### 3.4 Escalada para financeiro humano

**Atores:** Agente Nível-0 IA, Financeiro
**Wait time:** sim
**Humor:** 2

**Processo atual:**
Caso fora do padrão (disputa, negociação de prazo, inadimplência em análise) vai para o time financeiro. Só opera em horário comercial.

**Dores:**
- Aluno espera até o dia seguinte
- Financeiro recebe caso sem contexto completo
- Sem SLA claro comunicado ao aluno

**Sistemas:** Ticketing, CRM, ERP

**Artefatos:**
- Ticket escalado com contexto financeiro completo + SLA comunicado

**Tempo:** 2-3min | Espera: 2h-24h

**Assistente:** IA sugere handoff note para o financeiro
**Orquestração:** IA monta briefing estruturado (histórico de pagamentos, tentativas anteriores, tipo de disputa)
**Automação:** Escalada automática com threshold + agenda follow-up + notifica aluno do SLA

**Métricas:** Taxa de escalada para o financeiro, tempo até primeira resposta do financeiro
**Riscos:** Aluno se sente ignorado se o SLA não for explicitado

---

### 3.5 Negociação / resolução financeira

**Atores:** Financeiro, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Financeiro negocia prazo, desconto, parcelamento. Humano no controle por envolver decisão comercial e risco de receita.

**Dores:**
- Depende da disponibilidade do financeiro
- Política de desconto parcialmente verbal
- Variações por atendente do financeiro

**Sistemas:** ERP, WhatsApp Business, CRM

**Artefatos:**
- Acordo firmado + condições atualizadas no ERP

**Tempo:** 15-45min | Espera: variável conforme negociação

**Assistente:** IA sugere opções de acordo baseadas em histórico e política
**Orquestração:** IA propõe plano de negociação estruturado ao financeiro; humano decide
**Automação:** — (decisão humana mantida por risco comercial/legal)

**Métricas:** Taxa de fechamento de acordo, ticket médio pós-negociação, aderência à política
**Riscos:** Desvio de política de desconto; perda de receita por negociação ad-hoc

---

### 3.6 Registro financeiro e atualização multi-sistema

**Atores:** Agente Nível-0 IA, Financeiro
**Wait time:** não
**Humor:** 3

**Processo atual:**
Financeiro atualiza ERP manualmente com o acordo. Sync entre ERP, CRM e atendimento é lento — aluno recebe cobrança repetida já tendo acordado.

**Dores:**
- Falta de sincronia entre sistemas
- Cobrança duplicada após acordo = atrito com aluno
- Acordo verbal sem registro estruturado

**Sistemas:** ERP, CRM, Base de Conhecimento

**Artefatos:**
- Acordo registrado + contexto disponível para próximas interações

**Tempo:** 3-5min | Espera: 0

**Assistente:** IA sugere campos de registro ao financeiro
**Orquestração:** IA gera registro estruturado e atualiza múltiplos sistemas após validação
**Automação:** Registro automático + sync ERP/CRM + bloqueio de cobrança duplicada

**Métricas:** % acordos com registro completo, taxa de reincidência do atrito pós-acordo
**Riscos:** Erro de registro gera nova cobrança errada = grave atrito com aluno

---

## Fase 4: Escalonamento Técnico (2%)

### 4.1 Detecção de bug / incidente

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 1

**Processo atual:**
Aluno relata bug ("vídeo não carrega", "aula sumiu", "erro ao abrir"). Nível-0 decide se é bug real ou problema de uso.

**Dores:**
- Aluno descreve mal
- Atendente não é técnico, chuta severidade
- Bugs duplicados abertos em paralelo

**Sistemas:** WhatsApp Business, LMS, LLM

**Artefatos:**
- Issue identificada como técnica + severidade

**Tempo:** 3-5min | Espera: 0

**Assistente:** IA sugere se é bug real ou mau uso
**Orquestração:** IA distingue bug de problema de uso e define severidade
**Automação:** Classificação automática + busca em base de bugs conhecidos; se já reportado, devolve status ao aluno

**Métricas:** % bugs duplicados, acurácia de severidade, % resolvidos como "uso correto"
**Riscos:** Classificar bug crítico como simples e perder janela de ação

---

### 4.2 Coleta estruturada de evidências

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 2

**Processo atual:**
Atendente pede screenshots, versão do app, dispositivo, passos para reproduzir. Aluno resiste ou não sabe onde achar informações.

**Dores:**
- Aluno não sabe localizar versão do app
- Screenshots cortados ou borrados
- Atendente não insiste e manda dossiê incompleto

**Sistemas:** WhatsApp Business, LLM

**Artefatos:**
- Dossiê técnico (logs, prints, device info, passos de reprodução)

**Tempo:** 5-15min | Espera: variável

**Assistente:** IA sugere ao atendente o que pedir e como formular
**Orquestração:** IA guia a coleta passo-a-passo até ter dossiê completo
**Automação:** Coleta semi-automática (device via WhatsApp, logs via plataforma), só pede ao aluno o que não consegue sozinho

**Métricas:** Completude do dossiê, tempo até dossiê completo
**Riscos:** Evidência incompleta atrasa triagem do tech em dias

---

### 4.3 Abertura de ticket + comunicação de SLA

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** sim
**Humor:** 2

**Processo atual:**
Atendente abre ticket e promete "vamos te avisar". SLA raramente é comunicado ao aluno com clareza.

**Dores:**
- Aluno não sabe quando terá resposta
- Recontato recorrente pedindo atualização
- Backlog do tech pouco visível no front-office

**Sistemas:** Ticketing, WhatsApp Business

**Artefatos:**
- Ticket aberto (JIRA ou similar) com severidade + SLA comunicado ao aluno

**Tempo:** 2-3min | Espera: 1-5 dias (resolução do tech)

**Assistente:** IA redige ticket estruturado para atendente revisar
**Orquestração:** IA cria ticket com severidade/prioridade calculadas + informa SLA automaticamente ao aluno
**Automação:** Abertura + comunicação do SLA ponta-a-ponta; aluno recebe updates automáticos ao longo da jornada

**Métricas:** Tempo até abertura, clareza do SLA (medido via NPS), volume de recontatos antes da resolução
**Riscos:** SLA prometido que o tech não consegue cumprir (especialmente em período de transição do head of tech)

---

### 4.4 Triagem e resolução técnica

**Atores:** Tech
**Wait time:** sim
**Humor:** 3

**Processo atual:**
Tech analisa ticket, reproduz o bug, investiga e corrige. Com a saída do head of tech, o backlog cresceu e o replacement começa em 2 semanas.

**Dores:**
- Backlog crescente
- Transição de liderança técnica reduz capacidade
- Pouca visibilidade do status para o front-office

**Sistemas:** Ticketing, LMS (dev/prod), logs

**Artefatos:**
- Bug corrigido em prod ou workaround documentado

**Tempo:** 2-16h (trabalho técnico) | Espera: 1-5 dias

**Assistente:** IA sugere bugs parecidos e causas prováveis baseadas em histórico
**Orquestração:** IA prioriza backlog e gera resumo executivo dos issues ativos para o novo head de tech
**Automação:** — (resolução mantém humano no controle)

**Métricas:** MTTR, tamanho do backlog, % de bugs recorrentes
**Riscos:** Transição de liderança técnica prolonga resoluções

---

### 4.5 Comunicação da solução ao aluno

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Atendente avisa o aluno que o problema foi resolvido. Muitos não avisam — o fix vai para prod e o aluno não fica sabendo.

**Dores:**
- Retorno manual e inconsistente
- Aluno permanece com percepção de abandono
- Perde-se oportunidade de reengajamento

**Sistemas:** WhatsApp Business, Ticketing

**Artefatos:**
- Mensagem de resolução ao aluno + pedido de confirmação

**Tempo:** 2-3min | Espera: 0

**Assistente:** IA redige mensagem de comunicação
**Orquestração:** IA monta mensagem com contexto completo e envia após validação
**Automação:** Fechamento de ticket no tech dispara notificação automática ao aluno + convite NPS

**Métricas:** Taxa de notificação pós-fix, tempo entre fix e aviso, NPS dos casos técnicos
**Riscos:** Aluno sem resposta = percepção de abandono mesmo após fix

---

## Fase 5: Pós-atendimento e Aprendizado

### 5.1 Feedback / NPS do atendimento

**Atores:** Agente Nível-0 IA, Aluno
**Wait time:** não
**Humor:** 3

**Processo atual:**
Hoje praticamente não há NPS pós-atendimento no WhatsApp. Quando há, é enviado via outro canal com baixa taxa de resposta.

**Dores:**
- Sem feedback, sem loop de melhoria
- Qualidade percebida baseada em achismo
- Não há separação entre CSAT de nível-0, financeiro e técnico

**Sistemas:** WhatsApp Business, CRM

**Artefatos:**
- Nota CSAT + comentário livre

**Tempo:** 1min | Espera: 5min-24h (aluno responder)

**Assistente:** IA redige o convite ao NPS
**Orquestração:** IA envia NPS + follow-up com quem não respondeu
**Automação:** NPS automático pós-fechamento, com captura da nota + classificação automática de sentimento

**Métricas:** Taxa de resposta NPS, CSAT por categoria, % detratores
**Riscos:** Fadiga de pesquisa; alunos viciando respostas

---

### 5.2 Auditoria de qualidade do atendimento

**Atores:** Supervisor Suporte
**Wait time:** não
**Humor:** 4

**Processo atual:**
Hoje não há auditoria sistemática das conversas. Supervisor vê só o que for reportado como problema.

**Dores:**
- Atendentes inconsistentes
- Sem critério objetivo de qualidade
- Gaps só aparecem após reclamação escalada

**Sistemas:** WhatsApp Business, LLM, CRM

**Artefatos:**
- Amostra auditada + plano de correção

**Tempo:** 2-4h/semana | Espera: 0

**Assistente:** IA analisa amostra e destaca casos fora do padrão
**Orquestração:** IA audita 100% das conversas da IA, classifica por qualidade e reporta ao supervisor
**Automação:** Auditoria contínua com flagging automático de casos que merecem revisão humana

**Métricas:** Score de qualidade médio, % conversas auditadas, tempo de auditoria
**Riscos:** IA auditando IA sem controle humano gera blind spots

---

### 5.3 Enriquecimento da base de conhecimento

**Atores:** Agente Nível-0 IA, Supervisor Suporte
**Wait time:** não
**Humor:** 3

**Processo atual:**
Base é atualizada esporadicamente, quando alguém lembra ou supervisor cria artigo novo. Defasa rapidamente em relação à UI.

**Dores:**
- Base defasada em relação à plataforma atual
- Respostas antigas circulando
- Perguntas novas sem artigo correspondente

**Sistemas:** Base de Conhecimento, LLM

**Artefatos:**
- Artigos novos/atualizados + changelog

**Tempo:** 30min-2h por ciclo | Espera: 0

**Assistente:** IA sugere artigos a criar/atualizar com base em gaps detectados
**Orquestração:** IA gera draft de artigos novos a partir de conversas recorrentes; humano valida
**Automação:** Artigos criados automaticamente; supervisor revisa apenas a fila

**Métricas:** Cobertura da base vs. perguntas reais, taxa de uso de artigos, tempo até novo artigo publicado
**Riscos:** Artigos errados na base contaminam próximos atendimentos

---

### 5.4 Identificação de padrões e dores recorrentes

**Atores:** Supervisor Suporte, Head of Revenue
**Wait time:** não
**Humor:** 3

**Processo atual:**
Supervisor tenta extrair padrões manualmente (planilha, olhômetro). Decisões sobre produto e operação chegam devagar.

**Dores:**
- Relatórios mensais estáticos
- Sem drill-down dinâmico
- Dores reais do aluno raramente chegam em produto

**Sistemas:** CRM, LLM

**Artefatos:**
- Relatório de padrões + recomendações acionáveis

**Tempo:** 4-8h/mês | Espera: 0

**Assistente:** IA gera pré-análise para supervisor discutir
**Orquestração:** IA identifica clusters de dor, tendências e recomendações; Gabriel e supervisor priorizam em cadência mensal
**Automação:** Dashboard em tempo real com anomalias detectadas + alertas proativos

**Métricas:** Tempo até insight, nº de melhorias acionadas a partir do reporting, redução de dores mapeadas ao longo do tempo
**Riscos:** Dashboard sem ação = só número bonito

---

### 5.5 Reporting para Head of Revenue

**Atores:** Supervisor Suporte, Head of Revenue
**Wait time:** não
**Humor:** 3

**Processo atual:**
Relatório mensal em slides, com números agregados e anedotas. Gabriel pede drill-downs ad-hoc frequentemente.

**Dores:**
- Dados desatualizados na hora da decisão
- Sem contexto suficiente para decisão sobre dimensionamento do time
- Drill-down custa horas do supervisor

**Sistemas:** CRM, Dashboards

**Artefatos:**
- Report executivo (volume, CSAT, FCR, deflection rate, custo por atendimento)

**Tempo:** 2-4h/mês | Espera: 0

**Assistente:** IA gera draft do relatório mensal
**Orquestração:** IA consolida métricas + narrativa + recomendações para Gabriel revisar
**Automação:** Relatório automático semanal/mensal com narrativa executiva e alertas quando KPI sai da faixa

**Métricas:** Deflection rate (% absorvido pela IA), custo por atendimento, produtividade por atendente
**Riscos:** Relatório sem decisão = burocracia

---

## Resumo

| Fase | Steps | Assistente | Orquestração | Automação |
|------|-------|-----------|-------------|-----------|
| 1. Chegada e Triagem | 4 | Sugestão de match, categoria e rota | Consolidação de snapshot + ticket estruturado + roteamento validado | Boas-vindas, identificação, classificação e roteamento autônomos |
| 2. Resolução Nível-0 (90%) | 6 | Sugestões de diagnóstico, artigo, tag, follow-up | Condução de diagnóstico, geração de resposta e resumos de escalada | Diagnóstico IA, reset de senha autônomo, follow-up programado, fechamento auto |
| 3. Rota Financeira (8%) | 6 | Sugestões de categoria e opções de acordo | Briefing para financeiro, registro multi-sistema validado | Classificação, consulta, 2ª via/PIX/comprovante, sync ERP/CRM |
| 4. Escalonamento Técnico (2%) | 5 | Sugestões ao tech sobre bugs parecidos | Coleta guiada de evidências + briefing estruturado + priorização de backlog | Base de bugs, abertura de ticket, comunicação de SLA e notificação pós-fix |
| 5. Pós-atendimento | 5 | Drafts de NPS, artigos e reports | Auditoria e enriquecimento da base com validação humana | NPS automático, auditoria contínua, dashboard proativo, report auto |

### Destaques para o Gabriel

- **90% dos casos (acesso/login) têm automação total viável no curto prazo** — é onde o ROI é mais direto e a pressão de escala é maior
- **8% dos casos (financeiro) dividem-se entre "resposta padrão" (automatizável) e "negociação" (humano mantido)** — o ganho é proteger o financeiro para o que é decisão humana
- **2% dos casos (tech) são de orquestração e comunicação, não de resolução** — IA organiza, humano resolve; aluno recebe SLA claro e notificação ao final
- **Steps de wait time (gargalos) marcados em vermelho:** 2.5 (escalada ao nível-1), 3.4 (escalada ao financeiro), 4.3 (SLA ao aluno), 4.4 (resolução do tech). São os pontos onde aluno hoje fica sem visibilidade
- **Transição do head of tech (início em ~2 semanas)** afeta diretamente a capacidade do nível-4; reforça a urgência de absorver volume no nível-0

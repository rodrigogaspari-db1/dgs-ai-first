# Avaliação (Final) — Exercício 1.2 (Product Specialist) — Projeto NovaTech

> **Trilha:** Certificação AI First — DGS (DB1 Global Software)
> **Cenário:** 1 — Entendimento e Contexto
> **Papel:** Product Specialist
> **Exercício:** 1.2 — Design de jornada com componente de IA
> **Data da avaliação:** 2026-08-17

---

## Documentos utilizados na avaliação

**Skills e enunciado:**
- `cenario1avaliacaofoundation.md` — Skill de avaliação Foundation
- `cenario1avaliacaoproductspecialist.md` — Skill de avaliação do papel Product Specialist (critérios do Exercício 1.2)

**Entregável do participante:**
- `discoverysimuladostakeholdersnovatech.md` — Simulação de discovery com 6 stakeholders, conectada aos achados reais das Etapas 1-3, com mapa de risco de governança
- `jornadaatendentepropostanovatech.md` — Proposta de jornada melhorada (fluxo principal, fallback com 2 gatilhos, feedback loop, guardrails, papéis/SLAs, métricas de sucesso)
- `jornadaatendentediagramanovatech.html` — Diagrama visual de fluxo (Claude Design), com as 3 trilhas coloridas e os guardrails
- `transcricaochatjornadanovatech.md` — Transcrição completa do chat com o Claude

---

## Avaliação do Exercício 1.2 — Product Specialist

**Papel:** Product Specialist | **Cenário:** 1 — Entendimento e Contexto | **Exercício:** 1.2 — Design de jornada com componente de IA

### Resumo

Entregável forte e coerente: a jornada cobre os três fluxos pedidos com profundidade real (o fallback, inclusive, é subdividido em dois gatilhos distintos), os guardrails são amarrados a falhas concretas já encontradas nas etapas anteriores (não são genéricos), e o diagrama do Claude Design é claro, colorido por trilha e legível para público não-técnico. O ponto mais forte do conjunto é a continuidade: o participante usou os achados reais das Etapas 1-3 (conflito PROC-042, erro do FAQ item 45, gap do processo de sinistro) para simular um discovery e, a partir dele, desenhar a jornada — em vez de tratar este exercício como isolado.

### Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | Guardrails e fallback demonstram entendimento aplicado das limitações de LLM/RAG, não genérico: "declarar conflito entre fontes em vez de escolher uma versão silenciosamente" referencia diretamente o caso PROC-042; "sinalizar conteúdo não certificado" referencia diretamente o FAQ não validado. O feedback loop é entendido como mecanismo de manutenção contínua do RAG (sinalização → chamado ao dono → SLA → correção → remoção do aviso), não como recurso decorativo. |
| D2 — Uso de Ferramentas | 3 | Cadeia de construção visível e progressiva: discovery simulado (Claude) → jornada textual construída sobre o discovery (Claude) → diagrama construído sobre a jornada (Claude Design), cada etapa referenciando explicitamente o output da etapa anterior. Não é um prompt único; há encadeamento real de contexto entre os três artefatos. |
| D3 — Qualidade do Entregável | 3 | Completo, correto, especificamente aplicável à NovaTech e acionável: tabela de papéis/SLA por fonte, métricas de sucesso ligadas aos números reais do problema (12 min/chamado, ~19% escalonamento, nota 6,1), diagrama pronto para apresentação ao cliente. Um colega do time usaria este material sem pedir esclarecimentos adicionais. |
| D4 — Pensamento Crítico | 2 | O participante demonstra bom julgamento de produto ao decidir *conectar* o discovery e a jornada aos achados técnicos reais (em vez de tratar os dados do enunciado de forma isolada) — isso é orquestração de contexto, não passividade. Mas, como nos exercícios anteriores, não há evidência de verificação ou contestação independente de nenhum número ou afirmação específica gerada pela IA (ex.: os limiares de confiança propostos, ou os percentuais do discovery simulado) antes de aceitá-los como entregável final. |
| D5 — Aplicabilidade ao Projeto | 3 | Profundamente conectado: guardrails, fallback e feedback loop citam nominalmente PROC-042, item 45 do FAQ, processo de sinistro e o comitê de governança (Comercial, Operações, Compliance, Jurídico, TI, Atendimento) definido no discovery simulado. |

**Score do exercício: 2.8**

### Verificação de Armadilhas

Nenhuma armadilha explícita listada para este exercício na skill do papel (diferente do Exercício 1.1). Os quatro critérios de red flag (apenas caminho feliz / guardrails genéricos / botão sem processo atrás / diagrama genérico ou ausente) foram todos evitados — nenhum foi acionado.

### Pontos Fortes

- Guardrails rastreáveis a falhas reais já identificadas (ex.: "declarar conflito entre fontes" nasce diretamente do caso PROC-042), o que é exatamente o padrão de especificidade que a rubrica pede.
- Fluxo de feedback fechado de ponta a ponta — sinalização → chamado ao dono → SLA → aviso persistente → resolução → métricas para o comitê de governança — não é um botão sem processo por trás.
- Diagrama do Claude Design bem executado: três trilhas coloridas, legenda, guardrails na mesma peça, adequado tanto para o time técnico quanto para apresentação ao cliente.

### Pontos de Melhoria

- Incluir uma verificação humana independente de pelo menos um elemento gerado pela IA (por exemplo, questionar se os limiares de confiança Alta/Média/Baixa propostos fazem sentido operacional, ou recalcular os percentuais do discovery simulado) antes de aceitar o artefato como final — isso fortaleceria D4 de forma concreta.
- Os números do discovery simulado (34%/27%/21%/18%, escalonamento 19%) diferem dos fornecidos no enunciado original (35%/25%/20%/20%, escalonamento 15%) sem uma nota explícita explicando o recálculo — vale documentar que a diferença vem da extensão com os achados reais das Etapas 1-3, para deixar claro que não é uma inconsistência acidental.
- Deixar explícito no entregável que a simulação de discovery com 6 stakeholders e falas dedicadas é uma iniciativa voluntária além do mínimo pedido pelo enunciado (que citava apenas um parágrafo de dados) — isso evita a impressão de que os dados do discovery "vieram prontos" do cliente.

### Classificação

**Aprovado com distinção** (2.5–3.0)

### Tópicos da Trilha para Reforço

Não aplicável — score acima de 2.5. Vale manter o mesmo ponto de atenção observado no Exercício 1.1: registrar explicitamente ao menos um momento de verificação humana independente sobre outputs da IA, como prática de maturidade em D4.

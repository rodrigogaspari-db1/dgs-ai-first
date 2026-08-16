# Avaliação (Final) — Exercício 1.1 (Product Specialist) — Projeto NovaTech

> **Trilha:** Certificação AI First — DGS (DB1 Global Software)
> **Cenário:** 1 — Entendimento e Contexto
> **Papel:** Product Specialist
> **Exercício:** 1.1 — Mapeamento de intent com engenharia de contexto
> **Data da avaliação:** 2026-08-16
> **Status:** Substitui a avaliação preliminar (score 2.4) — refeita após anexação dos artefatos completos das 3 etapas e do texto ao cliente.

---

## Documentos utilizados na avaliação

**Skills e enunciado:**
- `cenario1avaliacaofoundation.md` — Skill de avaliação Foundation
- `cenario1avaliacaoproductspecialist.md` — Skill de avaliação do papel Product Specialist
- `enunciado1.1.md` — Enunciado completo do Exercício 1.1

**Entregável do participante:**
- `entregavel1.1productspecialisttranscricaochatnovatech.md` — Transcrição do chat com o Claude
- `etapa1visaogeralnovatech.md` — Mapa de temas e hipóteses de gaps (Etapa 1)
- `etapa2inconsistenciasproc042.md` — Análise de inconsistências PROC-042 v1 x v2 (Etapa 2)
- `etapa3cruzamentopol001slafaq.md` — Cruzamento POL-001, SLA-2024 e FAQ-Atendimento (Etapa 3)
- `textoclientediscoverygovernanca.md` — Texto ao cliente sobre discovery de governança e context rot

---

## Avaliação do Exercício 1.1 — Product Specialist

**Papel:** Product Specialist | **Cenário:** 1 — Entendimento e Contexto | **Exercício:** 1.1 — Mapeamento de intent com engenharia de contexto

### Resumo

Com os artefatos completos, o entregável se mostra consistentemente mais forte do que indicava a transcrição isolada: os três documentos de etapa são específicos, verificáveis e acionáveis (cálculos de impacto no frete conferidos em 15 combinações, contradição entre metadado de status e conteúdo operacional do PROC-042-v2, achado concreto de erro real em uso via item 45 do FAQ), e o texto ao cliente conecta de forma sofisticada a experiência de curadoria em pequena escala ao risco de *context rot* no acervo completo (~1.200 documentos). O principal ponto ainda em aberto é a ausência de evidência de verificação humana independente sobre os achados gerados pela IA.

### Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | Todos os critérios da skill do papel agora verificáveis nos documentos: estratégia de 3 etapas coerente e justificada (a Etapa 1 já sinaliza o conflito PROC-042 como "pergunta obrigatória ao cliente antes de qualquer indexação", justificando diretamente a escolha da Etapa 2); reflexão sobre volume/contexto presente e bem construída no texto ao cliente ("por que mais dados não significam mais qualidade", com o conceito de *context rot* aplicado corretamente à escala do acervo); riscos específicos e não-genéricos em todas as etapas. |
| D2 — Uso de Ferramentas | 3 | Ciclo gerar → avaliar → iterar mantido e agora com evidência completa: cada etapa amplia o contexto de forma calibrada (metadados → 2 documentos completos → FAQ + POL-001 + SLA-2024), com prompts que produzem outputs estruturados e sofisticados (tabelas de divergência, cálculo de 15 combinações região×peso). |
| D3 — Qualidade do Entregável | 3 | Os três documentos de etapa e o texto ao cliente são completos, corretos e diretamente utilizáveis por outro membro do time sem pedir esclarecimento — incluem tabelas, recomendações de ação, lista de próximos documentos a solicitar. Isso supera a limitação anterior (quando só havia resumos na transcrição). Ressalva: o mapa de riscos visual mencionado no chat (`mapa_risco_novatech.png`) nunca foi entregue nem incorporado a nenhum destes documentos — os riscos estão bem documentados em prosa/tabelas, mas não há o artefato consolidado que o enunciado nomeia. |
| D4 — Pensamento Crítico | 2 | Mantido em 2: os quatro documentos são outputs do Claude, não análise autoral do participante. O participante demonstra bom julgamento de produto ao *direcionar* a análise (ex.: redirecionar para o ângulo de governança e context rot no texto ao cliente), mas não há evidência de que ele tenha verificado, contestado ou corrigido de forma independente nenhum achado específico da IA (ex.: conferir manualmente o cálculo de impacto do frete ou a data de expiração da transição). |
| D5 — Aplicabilidade ao Projeto | 3 | Extremamente específico: percentuais exatos (0% a +12,5%), datas (01/12/2023, hoje 09/08/2026), seções de documentos citadas (POL-001 seção 3.2/3.5, SLA-2024 seção 1/2/3), stakeholders nomeados por área (Comercial, Operações, Compliance, Jurídico, SharePoint/Confluence). |

**Score do exercício: 2.8**

### Verificação de Armadilhas

| Armadilha | Identificada? |
|---|---|
| Fornecer os 5 documentos completos de uma vez no primeiro prompt (dispararia D1 ≤ 1) | **Não caiu na armadilha** — divulgação progressiva corretamente aplicada e agora comprovada nos "Insumo" de cada etapa (metadados → 2 docs completos → FAQ + demais). |

### Pontos Fortes

- Achado central da Etapa 2 (contradição entre o campo "Status" — que diz não haver vigência definida — e a Seção 5 da v2, que já opera como sucessora desde 01/12/2023) é exatamente o tipo de nuance não-óbvia que a rubrica valoriza, reforçado por cálculo quantitativo em todas as 15 combinações região × peso.
- O achado da Etapa 3 (item 45 do FAQ não bate nem com v1 nem com v2, com exemplo real de cliente prejudicado) demonstra domínio aplicado e conecta diretamente à necessidade de resolver o conflito de versionamento — não é um risco hipotético, é um erro comprovado em uso.
- O texto ao cliente traduz de forma acessível e tecnicamente correta o conceito de *context rot* e conecta a experiência da pequena amostra ao risco de escala no acervo de 1.200 documentos, propondo um discovery de governança concreto com stakeholders nomeados.

### Pontos de Melhoria

- Entregar o mapa de riscos consolidado (mencionado na transcrição como `mapa_risco_novatech.png`, mas nunca finalizado) — hoje os riscos estão bem descritos em texto/tabelas, mas faltou o artefato visual/consolidado explicitamente pedido no enunciado.
- Incluir uma seção de reflexão explícita comparando o resultado obtido com o cenário contrafactual de "colar os 5 documentos completos no primeiro prompt" — o texto ao cliente cobre o princípio geral (mais dados ≠ mais qualidade), mas não faz essa comparação direta pedida no item 4 da tarefa.
- Adicionar um passo de verificação humana independente sobre pelo menos um achado quantitativo do Claude (ex.: recalcular manualmente uma das 15 combinações de frete) antes de aceitá-lo como entregável final, para demonstrar julgamento próprio além da direção do processo.

### Classificação

**Aprovado com distinção** (2.5–3.0)

### Tópicos da Trilha para Reforço

Não aplicável — score acima de 2.5. Ainda assim, vale reforçar o hábito de registrar explicitamente pontos de verificação humana sobre outputs quantitativos da IA, como prática de maturidade em D4 para os próximos exercícios.

---

## Histórico de revisão

| Versão | Score | Classificação | Motivo |
|---|---|---|---|
| Preliminar | 2.4 | Aprovado | Baseada apenas na transcrição do chat (outputs resumidos, sem os documentos completos) |
| **Final** | **2.8** | **Aprovado com distinção** | Refeita com os 4 artefatos completos (Etapas 1-3 + texto ao cliente) anexados |

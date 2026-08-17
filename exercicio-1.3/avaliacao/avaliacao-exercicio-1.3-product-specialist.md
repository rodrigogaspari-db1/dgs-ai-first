# Avaliação do Exercício 1.3

> **Trilha:** Certificação AI First — DGS / DB1 Global Software
> **Papel:** Product Specialist
> **Cenário:** 1 — Entendimento e Contexto
> **Exercício:** 1.3 — Especificação de requisitos de RAG do ponto de vista do produto

---

## Resumo

Entregável de altíssima qualidade: os 33 requisitos cobrem as 5 áreas pedidas, estão ancorados em casos concretos da documentação NovaTech (PROC-042 v1/v2, FAQ 15/45, PROC-043, seguro de carga) e passaram por um ciclo de iteração real e bem documentado — versão inicial, revisão de testabilidade estruturada (papel de QA) e versão final com 23/33 requisitos corrigidos. O principal ponto fraco é a ausência, na transcrição, de um momento em que o próprio participante discorda ou corrige algo que o Claude produziu — a crítica ficou majoritariamente delegada ao próprio Claude atuando como "QA".

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | Entende que RAG depende de curadoria de dados, não só tecnologia (abre o doc dizendo isso explicitamente). Demonstra nuance real: distingue que o filtro de "resposta genérica" precisa ser uma checagem determinística fora do prompt (REQ-11), não uma instrução que o modelo "lembra" — isso é entendimento correto e específico de uma limitação real de LLMs, não um conceito genérico repetido. |
| D2 — Uso de Ferramentas | 3 | Ciclo gerar → avaliar → iterar plenamente visível: v1.0 (20 REQs) → incremento de negócio (REQ-21 a 33) → revisão de QA com metodologia de 4 critérios (✅/⚠️/❌) → edição de 23/33 requisitos, todos marcados 🆕 para revisão → consolidação v2.0. Prompts específicos e contextualizados em cada turno, não genéricos. |
| D3 — Qualidade do Entregável | 3 | Completo, correto, testável e acionável: convenções de formato mínimo definidas para viabilizar teste automatizado, dicionário de domínio proposto (REQ-12), sumário de rastreabilidade por tipo de verificação (Seção 12). Pontos numéricos não fechados (SLA, RTO/RPO, limiares) são marcados como "[a validar]" em vez de inventados — outro membro do time usaria isso sem pedir esclarecimentos adicionais. |
| D4 — Pensamento Crítico | 2 | Há julgamento próprio real: as três injeções de negócio no Turno 3 (pgvector, governança por perfil, hierarquia semântica) são decisões estratégicas do participante, não sugestões da IA, e o pedido de "destaque os critérios para eu revisar e aprovar" (Turno 5) mostra postura de gatekeeping, não aceitação cega. Porém a identificação dos gaps mais finos (os 17 requisitos não-testáveis) foi produzida pelo Claude no papel de QA, e a transcrição não registra nenhum momento em que o próprio participante discorda de ou corrige algo que o Claude gerou — falta essa evidência de crítica pessoal direta ao output. |
| D5 — Aplicabilidade ao Projeto | 3 | Profundamente conectado ao projeto: referencia PROC-042/v2, FAQ itens 3/15/22/38/45, POL-001, SLA-2024, tiers Gold/Silver/Standard, Azure AD/SharePoint já existentes, e os donos por área definidos no discovery (Operações, Comercial, Jurídico/Compliance). Nenhum requisito é genérico o suficiente para servir a qualquer outro projeto. |

**Score do exercício: 2.8**

---

## Verificação de Armadilhas

| Armadilha (presente na documentação-fonte / Anexo A) | Identificada? |
|---|---|
| PROC-042 v1 vs v2 — multiplicadores, fator de peso e prazo diferentes, nenhuma marcada como obsoleta | ✅ Sim — REQ-02, REQ-06, REQ-07, REQ-08 tratam o caso nominalmente |
| FAQ item 45 — mistura regra de desconto da v1 com valores da v2 vigente | ✅ Sim — REQ-09, tratado como "defeito de dado" com processo de auditoria |
| FAQ item 15 — tier "Platinum" inexistente (teste clássico de alucinação) | ✅ Sim — REQ-12, com dicionário de domínio fechado (tiers/regiões/classes) |
| FAQ itens 3/22 — exceção de carga perigosa e seguro de carga sem lastro documental oficial | ✅ Sim — REQ-05 exige identificação como FAQ não-validado, nunca como fato normativo |
| Gap — processo de sinistro (FAQ item 38) existe na prática mas não está formalizado no POL-001 | ✅ Sim — coberto pelo tratamento de "fontes fora do escopo documental" (REQ-05) e pelos pontos em aberto da Seção 8 |
| PROC-043 (cargas perigosas) em revisão pelo Compliance, pode nascer desatualizado | ✅ Sim — REQ-03, metadado `sujeito_a_revisao` com rebaixamento automático de confiança |

Nenhuma armadilha da documentação-fonte passou despercebida.

---

## Pontos Fortes

1. **Cobertura completa e ancorada em casos reais.** As 5 áreas pedidas (fontes, contradições, ausência de resposta, atualização, rastreabilidade) estão presentes e sempre referenciadas a casos concretos da NovaTech, nunca em linguagem genérica ("o assistente deve ser bom"), o que evita diretamente o red flag central desse exercício.
2. **Ciclo de iteração robusto e auditável.** Em vez de um "peça feedback genérico" superficial, o participante usou o Claude como revisor adversarial no papel de QA, com metodologia explícita de 4 critérios de testabilidade, produzindo uma diferença concreta entre v1 e v2 (23 de 33 requisitos corrigidos, cada um marcado para aprovação).
3. **Postura madura diante de decisões de negócio não fechadas.** SLA de revisão, RTO/RPO, limiar de expansão de busca — tudo fica marcado como "[a validar]" em vez de inventado, com um dicionário de domínio proposto para fechar a lacuna do caso "Platinum".

---

## Pontos de Melhoria

1. **Falta evidência de crítica pessoal direta ao output da IA.** A transcrição não registra nenhum momento em que o próprio participante rejeita ou corrige algo que o Claude produziu — toda a crítica fina (os 17 requisitos não-testáveis) foi gerada pelo Claude no papel de QA. *Ação sugerida:* registrar ao menos uma rodada em que o participante discorda de um ponto específico do Claude e pede um ajuste diferente do proposto, para demonstrar julgamento independente além da orquestração do processo.
2. **Seis requisitos permanecem "condicionalmente testáveis"** (REQ-09, REQ-15, REQ-24, REQ-26, REQ-32 e o caso b do REQ-10). *Ação sugerida:* propor um valor default hipotético (com etiqueta "proposta, a confirmar") para cada limiar numérico em aberto, em vez de deixar todos sem nenhuma referência de partida.
3. **Documento extenso (33 requisitos + 12 seções) para um público que inclui não-técnicos.** *Ação sugerida:* incluir um sumário executivo de uma página no início, já que Jurídico e Compliance também são consumidores diretos desta especificação.

---

## Classificação

**Aprovado com distinção** (2.8)

---

## Tópicos da Trilha para Reforço

Não aplicável — score acima de 2.5.

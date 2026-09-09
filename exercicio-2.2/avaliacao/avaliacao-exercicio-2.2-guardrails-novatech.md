# Avaliação do Exercício 2.2 — Definição de guardrails como artefato de produto

> **Trilha:** Certificação AI First — DGS / DB1 Global Software
> **Papel:** Product Specialist
> **Cenário:** 2 — Estruturação do Trabalho
> **Exercício:** 2.2 — Definição de guardrails como artefato de produto
> **Artefato avaliado:** `guardrails-novatech.md` (v1.1) + `historico-chat-guardrails-novatech.md`

---

## Resumo

O entregável é um documento de guardrails robusto, bem estruturado e fortemente ancorado no domínio NovaTech, com uma iteração real e bem documentada entre v1.0 e v1.1 (não cosmética). O ponto mais forte é a introdução da distinção Determinístico/Probabilístico com senso crítico genuíno — o participante questionou e o assistente corrigiu 3 de 14 classificações com justificativa sólida. O principal gap é a ausência de qualquer referência às decisões do Cenário 1 (ADRs, context budget), que rebaixa a dimensão de aplicabilidade.

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | Domina o conceito central do exercício (enforcement probabilístico vs determinístico) com nuance real: reconhece que os itens [D] só são de fato determinísticos se existirem campos estruturados confiáveis (`versao_status`, `categoria_carga`) — caso contrário, degradam para [P]. Isso é exatamente o tipo de sutileza contextual (não genérica) que a rubrica pede como diferencial de score 3. |
| D2 — Uso de Ferramentas | 3 | Há iteração documentada e substantiva: v1.0 (Turno 1) → discussão de classificação com discordâncias reais (Turno 2) → v1.1 (Turno 3) com mudanças concretas (seção 0.1, rótulos [D]/[P] em todos os 14 itens, seção 5 nova). Não é mudança cosmética — 3 dos 14 itens foram reclassificados com justificativa. Prompt específico e refinamento real, critério de score 3 do D2. |
| D3 — Qualidade do Entregável | 3 | Documento completo e correto: DEVE/NÃO DEVE/QUANDO EM DÚVIDA todos populados, rastreabilidade individual a incidentes, matriz de cobertura cruzada, matriz de classificação D/P. É prescritivo e acionável — outro membro do time (ou um agente) conseguiria aplicar as regras sem pedir esclarecimentos adicionais. |
| D4 — Pensamento Crítico | 3 | O ponto mais forte do exercício. O assistente não aceitou acriticamente a autoavaliação do usuário: discordou de D4, F2 e F4 com argumentação específica (ex.: F4 é por definição um caso não mapeado por regra fixa, logo é julgamento do modelo, não checagem estruturada). Também expôs explicitamente a limitação da própria classificação [D] (depende de premissa não confirmada sobre metadados do sistema). |
| D5 — Aplicabilidade ao Projeto | 2 | Fortemente conectado ao domínio NovaTech em superfície (PROC-042, versões v1/v2, SLA Gold, carga perigosa) — mas o documento não referencia nenhuma decisão do Cenário 1 (ADRs, context budget). A rubrica de score 3 exige explicitamente essa conexão com a fase anterior; sua ausência aqui é o gap que impede o score máximo, mesmo com boa aplicação ao domínio. |

**Score do exercício: 2.8**

---

## Verificação de Artefatos Machine-Readable

O documento é predominantemente prescritivo, não narrativo. Cada guardrail segue um padrão consistente e parseável: ID (D1, N2, F3...), rótulo [D]/[P], enunciado de regra, e linha `> Previne: INC-XX`. As tabelas de cobertura (seção 4) e classificação (seção 5) são estruturas tabulares que um agente ou script poderia consumir diretamente para checagem cruzada.

Ponto de atenção: alguns itens [P] (ex. D3, F3, F4) descrevem comportamento desejado em linguagem natural relativamente extensa ("deve buscar com múltiplas formulações...") — isso é esperado e correto para guardrails probabilísticos (não há como torná-los puramente estruturados), mas vale registrar que só os itens [D] são de fato "machine-enforceable" sem intervenção humana/prompt, algo que o próprio documento já reconhece na seção 0.1.

---

## Pontos Fortes

- Distinção D/P tratada com rigor técnico real, incluindo o reconhecimento de que a classificação depende de premissas de infraestrutura (metadados estruturados) ainda não confirmadas — isso é pensamento crítico de nível sênior, não apenas conformidade com o enunciado.
- Rastreabilidade completa e verificável: todos os 14 guardrails apontam para pelo menos 1 dos 3 incidentes, e a matriz de cobertura confirma que os 3 incidentes têm guardrail em cada uma das 3 categorias.
- Iteração genuína entre versões, com histórico do processo de correção preservado (Turno 2 documenta o "antes" da autoavaliação do usuário e o "depois" da correção do assistente).

## Pontos de Melhoria

- Conectar explicitamente ao menos 1–2 guardrails às decisões do Cenário 1 — por exemplo, se houve um ADR sobre "context budget" na fase anterior, isso poderia justificar por que D3 exige múltiplas reformulações de busca antes de declarar ausência (trade-off custo/token vs. completude).
- A seção 0.1 introduz uma premissa crítica (metadados estruturados) mas não propõe um próximo passo concreto além de "recomenda-se confirmar com o time técnico" — poderia amadurecer isso em uma ação/critério de verificação explícito para o exercício 2.3 (AGENTS.md), fechando o ciclo entre os exercícios.
- Nenhuma menção a como esses guardrails se relacionam com a linguagem ubíqua definida no exercício 2.1 (se aplicável neste histórico) — reforçaria a coerência entre entregáveis do mesmo cenário.

---

## Classificação

**Aprovado com distinção** (2.8)

---

## Tópicos da Trilha para Reforço

Não aplicável — score acima de 2.5. Recomenda-se, para os próximos exercícios do cenário (2.3), reforçar apenas a prática de referenciar explicitamente artefatos de fases anteriores (ADRs, context budget) ao construir novos entregáveis, garantindo continuidade entre cenários.

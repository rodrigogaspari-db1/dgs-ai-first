# Avaliação do Exercício 2.2 — Governança de specs no modelo SDD

> **Trilha:** Certificação AI First — Engenharia de Software Agêntica (DGS / DB1 Global Software)
> **Papel avaliado:** Product Specialist
> **Cenário:** 2 — Estruturação do Trabalho
> **Exercício:** 2.2 — Governança de specs no modelo SDD
> **Entregáveis avaliados:** `processo-governanca-specs-novatech.md` (v1.1), `specs-tracker-novatech-offline.html`, `transcricao-sessao-governanca-specs-tracker-novatech.md`

---

## Nota metodológica prévia

A tabela de critérios para "Exercício 2.2" na skill `avaliacao-product-specialist.md` descreve um exercício diferente (Guardrails formalizados / DEVE-NÃO DEVE-QUANDO EM DÚVIDA / rastreabilidade a 3 incidentes) do que o `enunciado-2.2.md` efetivamente pede (processo de governança de specs + board Cowork + change management). Há um descompasso de numeração entre os documentos fornecidos. Por isso, esta avaliação usou com rigor as **5 dimensões da Foundation** (framework comum, independente do exercício específico) e os **critérios de avaliação explícitos do próprio enunciado-2.2.md**, em vez de forçar o encaixe na tabela de "Guardrails formalizados" que não corresponde ao entregável.

---

## Resumo

Entregável muito sólido: o documento de governança é prescritivo, versionado, com matriz RACI, três Gates com checklists de saída, convenção de commit e proposta de guardrail para o `AGENTS.md`; o board (Kanban + Tabela, exportado em HTML offline) é prático e reflete o estado real do projeto. A integração com as decisões do Cenário 1/fase anterior (ADRs, VCs, bounded contexts, Anexo C) é profunda e consistente ao longo de todo o texto.

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | **3** | Aplica SDD com nuance real: Gate como checkpoint que trava a próxima etapa (Seção 4), versionamento minor/major com congelamento na aprovação e cascata automática para "Requer Revisão" quando um artefato-fonte muda depois de já consumido (Seção 7) — não é um conceito genérico de "versão", é uma regra de propagação de invalidação. Distingue corretamente conteúdo (o quê os requirements/plan/tasks dizem) de processo de governança (como eles são produzidos/aprovados), e usa "papel ≠ pessoa" na matriz RACI para preservar separação de responsabilidade mesmo com acúmulo de papéis. |
| D2 — Uso de Ferramentas | **3** | Iteração real e documentada: v1.0 → v1.1 remove duas estruturas de pasta propostas (`reviews/`, `_ledger/gates.md`) e adiciona a Seção 1 (Convenções) — mudança estrutural, não cosmética, com "Nota de revisão" explicando o porquê. No Cowork, há calibração deliberada de tratamento visual antes de gerar ("painel de controle/checkpoint de logística") e o board foi populado com o estado real do projeto (query-endpoint v3.0, 14 VCs, 2 rodadas de correção) em vez de dados genéricos — evidência de prompt específico e output refinado, não aceitação acrítica de um primeiro rascunho. |
| D3 — Qualidade do Entregável | **3** | Completo e acionável: nomenclatura de arquivos, localização no repositório (sem inventar pastas fora do Anexo C), cabeçalho de versionamento padronizado, convenção de commit Git funcional (`spec(<slug>): ...`), checklist resumido por Gate. Uma ressalva real: o "change management pós-implementação" (item 3 do enunciado) é resolvido apenas *implicitamente* pela regra de major version + "Requer Revisão" em cascata (Seção 7) — não há um fluxo explícito do que acontece com tasks já em código quando o requirements.md muda depois do Gate 3. |
| D4 — Pensamento Crítico | **3** | Seção 12 (Riscos) é análise própria genuína, não decorativa: identifica que o próprio processo pode burocratizar, que papel concentrado pode mascarar separação de responsabilidade, que a consolidação via Git é fragilizada por disciplina de commit, e — o ponto mais forte — reconhece explicitamente um caso que o próprio processo **não cobre** (módulo sem bounded context de origem clara). Também é transparente ao extrapolar o pedido original (Gate 3 "não fazia parte da pergunta original... incluído por completude"), assumindo o risco à vista em vez de escondê-lo. |
| D5 — Aplicabilidade ao Projeto | **3** | Referencia constantemente artefatos reais do projeto: `requirements.md` do query-endpoint (v3.0, 14 VCs), as duas revisões de coerência já produzidas (scope boundaries, verification criteria), o Recorte de Domínio v2.0 e seus BCs, o plano de medição do O9, `docs/adr/`, e as Constraints C1–C6. Não é um processo genérico "que serviria para qualquer projeto" — está amarrado à estrutura do Anexo C e ao histórico real de decisões da Fase 1/2. |

**Score do exercício: 3.0**

---

## Verificação de Artefatos Machine-Readable

O documento é majoritariamente prescritivo, com trechos claramente pensados para consumo por agente/script:

- **Bom:** o guardrail da Seção 10 é imperativo puro — *"Nunca gerar `plan.md` para um módulo cujo `requirements.md` não esteja com `Status: Aprovado`"* — um agente conseguiria seguir sem interpretação; a convenção de commit da Seção 8 (`spec(<slug>): <artefato> <versão> — <gate ou motivo>`) é um template literal, parsável por `git log --grep`; o cabeçalho de versionamento (Seção 7) é um bloco de campos fixos (`Status:`, `Aprovador:`), não prosa.
- **Narrativo (mas apropriado):** a Seção 2 ("Por que isto precisa de processo") é justificativa em prosa — correto que seja, pois não se destina a ser seguida por um agente, apenas a dar contexto humano. Não há confusão entre as duas camadas.

---

## Pontos Fortes

- Disciplina notável de **não inventar estrutura**: a v1.1 recua explicitamente das duas pastas novas propostas na v1.0, tratando a estrutura já aprovada do Anexo C como invariante — isso é maturidade de processo, não só correção técnica.
- O board conecta Gates a papéis de forma visualmente clara (colunas de Gate rotuladas com quem aprova) e usa estado real do projeto em vez de dados fictícios genéricos.
- Autoavaliação de limitações genuína na Seção 12, incluindo um caso que o próprio processo deliberadamente não resolve.

---

## Pontos de Melhoria

- Tornar explícito o fluxo de "spec muda depois de já estar em implementação" (item 3 do enunciado) como uma subseção dedicada — hoje a resposta existe, mas está diluída dentro da regra geral de versionamento major, o que exige inferência do leitor.
- A atribuição de autoria do documento a "Delivery Manager + Tech Lead" (distinta do Product Specialist) é uma separação conceitualmente interessante entre "dono do conteúdo" e "dono do processo de governança", mas vale confirmar com o enunciado se essa é a leitura pretendida do papel, já que o exercício foi atribuído ao Product Specialist.
- O board é uma visualização estática (HTML/CSS com radio buttons) — para uso real como ferramenta de tracking, valeria indicar explicitamente que atualizações futuras exigem reenvio de arquivo (o texto já sinaliza isso no rodapé, o que é positivo, mas reforça que não é um sistema vivo).

---

## Classificação

**Aprovado com distinção (2.5–3.0)**

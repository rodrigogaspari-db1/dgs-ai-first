# Avaliação do Exercício 2.3 — Product Rules & Guardrails (AGENTS.md)

> **Programa:** Trilha de Certificação AI First — DGS / DB1 Global Software
> **Papel:** Product Specialist
> **Cenário:** 2 — Estruturação do Trabalho
> **Exercício:** 2.3 — Participação na construção do AGENTS.md do projeto (seção "Product Rules & Guardrails")
> **Data da avaliação:** 2026-09-14
> **Entregável avaliado:** `agents-md-product-rules-guardrails.md` (v1.1)

---

## Resumo

Entregável de altíssima qualidade: machine-readable desde a raiz (bloco YAML na Seção 0), regras prescritivas com verbo + efeito verificável separados da justificativa, glossário construído para reduzir erro de LLM (não é lista de definições genéricas), e amarração explícita ao Cenário 1 (ADR-0002, ADR-0003) que corrige diretamente a ressalva recebida no Exercício 2.2. A iteração v1.0 → v1.1 é substantiva, documentada critério a critério, sem alterações cosméticas.

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|:-----:|---------------|
| D1 — Domínio Conceitual | 3 | Domina AGENTS.md como artefato prescritivo/machine-readable (não narrativo), SDD (referência a `requirements.md`), e a classificação determinístico/probabilístico com nuance real: reconhece que D4/F4 são "`[D]` condicional" dependente de metadados (`versao_status`, `contradicao_pendente`) — exatamente o tipo de sutileza que o critério "3" exige. |
| D2 — Uso de Ferramentas | 3 | Ferramenta correta para o exercício (Claude chat, conforme enunciado — Copilot não era exigido aqui, então a regra de corte de D2≤1 por ausência de teste com Copilot não se aplica). A iteração v1.0→v1.1 é documentada com uma mudança concreta por critério (bloco YAML, separação comando/justificativa, coluna de risco de confusão no glossário, pares DO/DON'T, processo de versionamento) — não é v1≈v2. |
| D3 — Qualidade do Entregável | 3 | Completo, correto, específico ao NovaTech e acionável: um outro membro do time (ou o Copilot) conseguiria seguir sem pedir esclarecimento. IDs sincronizados entre YAML e tabelas, enums fechados, schema `AssistantResponse` com exemplos DO/DON'T. |
| D4 — Pensamento Crítico | 3 | Vai além da aceitação acrítica: sinaliza explicitamente riscos residuais (N1 e D3 permanecem `[P]` por falta de grounding-check/detecção de idioma), assume e marca como pendente de validação o caminho `docs/product/guardrails.md` (não documentado no Anexo C), e trata ausência de propagação de metadados como "bug de guardrail", não como detalhe técnico. |
| D5 — Aplicabilidade ao Projeto | 3 | Profundamente conectado: referencia ADR-0002 e ADR-0003 como pré-condição para que as regras `[D]` condicionais funcionem de fato — resposta direta à lacuna de D5 apontada na avaliação do Exercício 2.2. Usa linguagem ubíqua do domínio (carga perigosa, CT-e, multiplicador regional) e respeita a estrutura do repositório do Anexo C (`specs/`, `docs/adr/`, `tests/fixtures/`). |

### Score do exercício: **3.0**

---

## Verificação de Artefatos Machine-Readable

Sim, plenamente prescritivo. O bloco YAML da Seção 0 é parseável por linter/CI sem depender de interpretação de texto em prosa (`id`, `category`, `enforcement`, `statement`, `prevents_incident`), e as tabelas das Seções 1–2 mantêm os mesmos IDs, funcionando como camada legível para humanos do mesmo dado — não como uma segunda fonte de verdade divergente. As restrições de código (Seção 3) usam pares DO/DON'T em TypeScript reais, não descrição textual ("evite hardcoding"), o que de fato influenciaria a geração do Copilot. Não há nenhum trecho narrativo que precisasse ser reescrito para virar regra.

---

## Pontos Fortes

1. **Rastreabilidade de duas pontas**: cada regra aponta para um incidente (INC-01/02/03) *e* para a pré-condição de infraestrutura (metadados do índice) que determina se ela é de fato `[D]` ou regride a `[P]` — evita a armadilha de classificar como determinístico algo que só é determinístico na teoria.
2. **Glossário funcional, não decorativo**: a coluna "Risco de confusão para o LLM" justifica a existência de cada termo com um mecanismo de erro concreto (ex: "Platinum" por analogia com programas de fidelidade genéricos).
3. **Seção 5 (controle de versão)** trata a própria seção como artefato vivo, com changelog já populado e regra de que toda mudança exige origem documentada (incidente, ADR ou aprovação do Tech Lead).

---

## Pontos de Melhoria

1. A suposição sobre o caminho `docs/product/guardrails.md` está corretamente sinalizada como pendente, mas poderia já vir acompanhada de uma pergunta explícita ao Tech Lead dentro do próprio documento (ex: um comentário `<!-- TODO: confirmar path -->`), tornando a pendência visível também para quem só lê o artefato final, sem acesso ao histórico de sessão.
2. N1 e D3 permanecem `[P]` sem mecanismo de verificação — está bem documentado como risco residual, mas o artefato poderia esboçar o *próximo passo* (ex: "grounding-check via comparação de valores extraídos contra fixtures" como item de backlog), fechando o ciclo entre "risco identificado" e "ação de mitigação proposta".
3. A Seção 3.5 menciona `indexer.ts` e `search.ts` como responsáveis por propagar os metadados, mas não referencia explicitamente qual spec (`specs/pipeline-ingestao/plan.md`, já citado na Seção 4) deveria formalizar esse contrato — uma referência cruzada ali fecharia o laço.

---

## Classificação

**Aprovado com distinção** (2.5–3.0)

---

## Tópicos da Trilha para Reforço

Não aplicável — score ≥ 2.5.

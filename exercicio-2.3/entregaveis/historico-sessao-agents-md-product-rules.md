# Histórico de Sessão — Cenário-Âncora 2 (IA First Certification)

**Projeto:** NovaTech Assistant — Fase de Estruturação do Trabalho
**Papel exercido:** Product Specialist
**Exercício:** 2.3 — Participação na construção do AGENTS.md do projeto (seção "Product Rules & Guardrails")
**Ferramenta:** Claude (chat)
**Data da sessão:** 2026-09-14

---

## 1. Objetivo da sessão

Contribuir com a seção **"Product Rules & Guardrails"** do `AGENTS.md` do repositório `novatech-assistant`, conforme solicitado pelo Tech Lead, cobrindo: regras de comportamento do assistente, glossário de linguagem ubíqua, restrições de geração de código, e referências a documentos de spec — e revisar essa seção contra os critérios de avaliação do exercício.

---

## 2. Insumos revisados no início da sessão

Antes de produzir qualquer artefato, o contexto do projeto foi revisado e consolidado:

- **Anexo A — Documentação Simulada da NovaTech**: POL-001 (devolução), PROC-042 v1/v2 (frete especial, com contradições não resolvidas entre multiplicadores regionais, fator de peso e prazo adicional), SLA-2024 (tiers Gold/Silver/Standard, incidentes críticos, penalidades) e FAQ-Atendimento (informal, não validado por Compliance).
- **Anexo C — Estrutura do Repositório**: árvore de diretórios do `novatech-assistant` (specs/, skills/, docs/adr/, src/, tests/), convenções de nomenclatura e exemplo de `.mcp/mcp.json` local.
- **Exercício 2.2 (Product Specialist) — Guardrails formalizados**: documento v1.1 com 14 guardrails (D1–D5, N1–N4, F1–F4) organizados em DEVE / NÃO DEVE / QUANDO EM DÚVIDA, classificados como determinísticos (`[D]`) ou probabilísticos (`[P]`), rastreados aos incidentes INC-01, INC-02 e INC-03. Avaliação anterior: 2,8/3,0 — "Aprovado com distinção", com ressalva no critério D5 (Aplicabilidade ao Projeto) por falta de referência explícita às decisões do Cenário 1.
- **Exercício 2.3 (documento de estruturação)**: estrutura oficial do `AGENTS.md` proposta pelo Tech Lead (7 seções) e o enunciado específico da tarefa do Product Specialist, com os guardrails simulados fornecidos como insumo autossuficiente.

**Pendência identificada e registrada:** a seção "Coding Standards (Tech Lead)" ainda não tem exercício/insumo correspondente carregado nesta sessão.

---

## 3. Artefato produzido: seção "Product Rules & Guardrails"

### Versão 1.0 (primeira entrega)
Estrutura entregue com 4 blocos, conforme solicitado:
1. Regras de comportamento (DEVE / NÃO DEVE / QUANDO EM DÚVIDA), cada uma com classificação de enforcement (`[D]`/`[P]`, incluindo casos condicionais dependentes de metadados do índice) e rastreabilidade a INC-01/02/03.
2. Glossário de linguagem ubíqua (tiers de cliente, carga perigosa, frete especial, multiplicador regional, SLAs, CT-e, documento normativo vs. informal, versão vigente vs. obsoleta).
3. Restrições de geração de código: schema `AssistantResponse` obrigatório, trava determinística de carga perigosa, enum fechado de tier, proibição de hardcoding de valores de negócio.
4. Tabela de referências a specs, ADRs e fixtures no repositório.

### Versão 1.1 (revisão solicitada)
Revisão feita contra 5 critérios explícitos do exercício, com as seguintes mudanças:

| Critério avaliado | Ajuste aplicado |
|---|---|
| Seção machine-readable | Adicionado bloco YAML estruturado (Seção 0) com `id`, `category`, `enforcement`, `statement` e `prevents_incident` por regra, consumível por linters/agentes sem parsing de tabela. |
| Regras claramente prescritivas (DEVE/NÃO DEVE) | Separado o comando (verbo + efeito verificável) da justificativa de negócio, antes misturados na mesma célula da tabela. |
| Glossário útil para um LLM | Adicionada coluna "Risco de confusão para o LLM", explicitando por que cada termo geraria erro sem definição (ex: "Gold" confundido com metal/tier genérico; "frete especial" generalizado incorretamente). |
| Restrições de código concretas o suficiente para o Copilot | Adicionados pares de código **DO/DON'T** em TypeScript para cada restrição (schema de resposta, validação pré-LLM, enum fechado, ausência de hardcoding). |
| Controle de versão da seção | Criada Seção 5 nova: processo de versionamento (patch/minor/major), exigência de origem documentada para toda mudança de regra (incidente, ADR ou aprovação do Tech Lead), e tabela de changelog já populada (v1.0 → v1.1). |

**Arquivo final entregue:** `agents-md-product-rules-guardrails.md` (v1.1), pronto para ser incorporado ao `AGENTS.md` do repositório na seção correspondente.

---

## 4. Decisões e observações relevantes para continuidade

1. **Rastreabilidade Cenário 1 → Cenário 2 reforçada**: a seção incorpora explicitamente ADR-0002 (context budget) e ADR-0003 (tratamento de contradições) como pré-condição para que as regras `[D]` condicionais (D4, F4) de fato funcionem de forma determinística — isso responde diretamente à ressalva de D5 recebida na avaliação do Exercício 2.2.
2. **Risco em aberto, sinalizado no artefato**: as regras N1 (não inventar valores numéricos) e D3 (idioma) permanecem `[P]` — não há mecanismo de verificação automatizada (grounding-check ou detecção de idioma) implementado nesta fase; ficou registrado como risco residual conhecido, não como falha do artefato.
3. **Suposição pendente de validação**: o caminho `docs/product/guardrails.md` foi assumido como localização do documento-fonte de guardrails, pois o Anexo C não define esse caminho explicitamente. Deve ser confirmado com o Tech Lead ou ajustado quando o repositório real for montado.
4. **Consistência de IDs**: os identificadores de regra (D1–D5, N1–N4, F1–F4) são compartilhados entre o bloco YAML (Seção 0) e as tabelas legíveis por humano (Seção 1) — qualquer edição futura deve manter essa sincronia para não quebrar o parsing automatizado.

---

## 5. Estado ao final da sessão

- Seção "Product Rules & Guardrails" concluída na v1.1 e disponível como arquivo `.md` para incorporação ao repositório.
- Próxima ação em aberto (não iniciada nesta sessão): consolidação das demais seções do `AGENTS.md` (Coding Standards, Testing Standards, Project Management Rules, Build & Deploy) e integração final do documento único pelo Tech Lead.

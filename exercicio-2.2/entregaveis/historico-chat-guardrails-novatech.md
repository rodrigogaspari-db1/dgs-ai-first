# Histórico do Chat — Projeto NovaTech (Guardrails)

**Projeto:** Prática de IA First — Simulação NovaTech
**Tema da sessão:** Formalização de guardrails informais + classificação Determinístico/Probabilístico

---

## Turno 1 — Usuário

Continuando o projeto da prática de IA first - simulação do projeto NovaTech, agora preciso que você avalie as atividades do exercício anterior (dentro deste projeto do claude) aonde foram criados guardrails informais:

```markdown
(1) Sempre citar fonte.
(2) Nunca inventar prazos ou valores.
(3) Quando não encontrar resposta, dizer explicitamente.
(4) Responder em português formal.
```

e também 3 ocorrências de incidentes registrados nos testes:

```markdown
1. "O assistente respondeu que o prazo de devolução para carga perigosa é 7 dias, quando na verdade cargas perigosas NÃO podem ser devolvidas."
2. "O assistente citou 'PROC-042, seção 2' mas os multiplicadores informados eram da versão 1 (desatualizada), não da v2 (vigente)."
3. "O assistente disse 'Não encontrei informação sobre isso' para uma pergunta sobre SLA Gold, mas o documento SLA-2024 estava indexado e continha a resposta."
```

Pedido: reforçar os guardrails informais e reescrevê-los/formalizá-los em um documento oficial de guardrails do projeto, com a estrutura:

```markdown
**DEVE** (comportamentos obrigatórios).
**NÃO DEVE** (comportamentos proibidos).
**QUANDO EM DÚVIDA** (comportamentos de fallback).
```

Critérios exigidos:
1. Cada guardrail deve estar ligado a pelo menos um dos 3 incidentes (indicando qual incidente previne).
2. Os guardrails devem ser específicos ao domínio da NovaTech, não genéricos.

---

## Turno 1 — Assistente

Criou o documento oficial **v1.0** (`guardrails-novatech.md`) contendo:

- **Seção 0 — Origem e rastreabilidade:** tabela resumindo os 3 incidentes (INC-01: devolução de carga perigosa; INC-02: versão desatualizada do PROC-042; INC-03: falso negativo sobre SLA Gold) e a falha de fundo de cada um.
- **Seção 1 — DEVE:** D1 a D5, cada um com a nota "Previne: INC-XX".
- **Seção 2 — NÃO DEVE:** N1 a N4, idem.
- **Seção 3 — QUANDO EM DÚVIDA:** F1 a F4, idem.
- **Seção 4 — Matriz de cobertura:** tabela cruzando cada incidente com os guardrails que o previnem, confirmando que os 3 incidentes têm cobertura nas 3 categorias.

Arquivo entregue via `present_files`.

---

## Turno 2 — Usuário

Pedido de classificação de cada guardrail como **Probabilístico (P)** ou **Determinístico (D)**, com autoavaliação do usuário:

```
D1. D   D2. P   D3. P   D4. P   D5. P
N1. P   N2. D   N3. D   N4. P
F1. D   F2. D   F3. P   F4. D
```

Pedido: conferir se a classificação está correta e, se não, justificar.

---

## Turno 2 — Assistente

Apresentou o critério de classificação:
- **Determinístico:** resposta objetivamente única e verificável, normalmente via metadado estruturado (ex.: campo de versão, campo de categoria de carga).
- **Probabilístico:** depende de julgamento/interpretação de linguagem natural, sem critério fixo verificável.

Confirmou 10 dos 14 itens da avaliação do usuário (D1, D2, D3, D5, N1, N2, N3, N4, F1, F3) e discordou de **3 itens**:

- **D4** (citar fonte com doc + seção + versão): usuário marcou P, assistente reclassificou como **D** — completude da citação é um check de formato/estrutura, verificável objetivamente.
- **F2** (carga perigosa + "não houver certeza da regra aplicável"): usuário marcou D, assistente reclassificou como **P** — o gatilho é a autoavaliação de incerteza do modelo, não um campo estruturado.
- **F4** (catch-all para situações não cobertas pelos itens anteriores): usuário marcou D, assistente reclassificou como **P** — por definição é um caso não mapeado por regra fixa; reconhecer a situação e evitar suposição é julgamento do modelo.

Alertou que os itens marcados **D** (D1, N2, N3, F1) só são de fato determinísticos **se existirem campos estruturados confiáveis** no sistema da NovaTech (ex.: `versao_status`, `categoria_carga`); caso contrário, também seriam P na prática.

Apresentou tabela final revisada:

| Categoria | Determinístico (D) | Probabilístico (P) |
|---|---|---|
| DEVE | D1, D4 | D2, D3, D5 |
| NÃO DEVE | N2, N3 | N1, N4 |
| QUANDO EM DÚVIDA | F1 | F2, F3, F4 |

---

## Turno 3 — Usuário

Concordou com as correções e pediu para gerar uma nova versão do documento oficial já com a classificação (D/P) corrigida em cada guardrail.

---

## Turno 3 — Assistente

Atualizou o arquivo para **v1.1** (`guardrails-novatech.md`), com as seguintes adições:

- Cabeçalho atualizado (Versão 1.1, nota sobre a novidade da versão).
- **Seção 0.1 — Critério de classificação:** definição de [D] e [P], mais o alerta sobre a premissa de metadados estruturados para os itens [D].
- Rótulo **[D]** ou **[P]** inserido no título de cada um dos 14 guardrails (D1–D5, N1–N4, F1–F4), refletindo as correções do Turno 2 (D4 → D, F2 → P, F4 → P).
- **Seção 5 — Matriz de classificação (Determinístico x Probabilístico):** tabela por categoria + leitura prática, destacando que a maioria dos guardrails (9 de 14) é probabilística e recomendando testes de regressão contínuos além de checagem automática pontual.

Arquivo entregue via `present_files`.

---

## Turno 4 — Usuário

Pedido atual: gerar um arquivo `.md` com todo o histórico deste chat.

## Turno 4 — Assistente

Este arquivo.

---

## Artefatos gerados nesta sessão

| Arquivo | Descrição |
|---|---|
| `guardrails-novatech.md` | Documento oficial de guardrails do projeto NovaTech, versão 1.1 (com classificação D/P) |
| `historico-chat-guardrails-novatech.md` | Este histórico da conversa |

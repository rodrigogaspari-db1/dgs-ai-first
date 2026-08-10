# Análise de Artefatos — NovaTech
## Etapa 3: Cruzamento entre POL-001, SLA-2024, FAQ-Atendimento e achados das Etapas 1-2

**Data:** 2026-08-09
**Insumo:** POL-001 v3.1 (política de devolução), SLA-2024 v2024.1 (tabela de SLA) e 8 itens do FAQ-Atendimento (de um total de 47)

---

## 1. Validações positivas — FAQ confirmado pelos documentos oficiais

Três dos oito itens analisados batem exatamente com a documentação normativa, o que é uma boa notícia: mostra que o conhecimento prático do time de atendimento não é aleatório, tem boa aderência ao oficial nesses pontos.

| Item do FAQ | Conteúdo | Confirmado por |
|---|---|---|
| 3 — Devolução de carga perigosa | Não elegível pelo processo padrão; encaminhar ao ramal 4500 (Gestão de Riscos) para tratamento individual | POL-001, seção 3.2 — mesma exceção, mesmo ramal |
| 15 — Tier "Platinum" | Não existe; tiers são Gold, Silver e Standard | SLA-2024, seção 1 — "não existem outros tiers além dos três listados" |
| 41 — SLA de resposta x resolução | Gold 2h/24h, Silver 4h/48h, Standard 8h/72h | SLA-2024, seção 2 — valores idênticos (FAQ omite apenas a qualificação "horas úteis" e os prazos de incidente crítico) |

---

## 2. Inconsistências encontradas

### 2.1. Desconto por volume — o FAQ não bate nem com a v1 nem com a v2 (achado crítico)

O item 45 do FAQ diz: *"Para clientes com mais de 10 fretes especiais por mês, existe desconto automático na tabela."*

Comparando com a Etapa 2:
- **v1** (PROC-042): desconto a partir de **10 fretes/mês**, mas **negociado** pelo Comercial e registrado em aditivo — não é automático.
- **v2** (PROC-042-v2, que a Etapa 2 recomendou como versão vigente): desconto **automático** de 5%, mas a partir de **8 fretes/mês** (não 10), com 10% acima de 15 fretes/mês.

O FAQ mistura o limiar da v1 (10 fretes) com a mecânica da v2 (desconto automático) — uma terceira regra, que não corresponde a nenhum dos dois documentos oficiais. **Isso é evidência concreta de que o problema de versionamento do PROC-042 já está gerando informação incorreta em uso real pelos atendentes**: um cliente com 8 ou 9 fretes especiais/mês, que segundo a v2 já teria direito a 5% de desconto automático, está sendo informado (via este FAQ) que não tem direito a desconto algum. Esse achado reforça a prioridade da recomendação da Etapa 2 de formalizar qual versão é vigente.

### 2.2. Limiar de prioridade em chamados de rastreamento

O item 27 do FAQ orienta classificar como "prioridade alta" chamados de rastreamento quando o cliente é Gold **ou** o valor da carga é acima de **R$ 50.000**.

A definição oficial de "incidente crítico" no SLA-2024 (seção 3) usa um critério diferente: carga com valor declarado acima de **R$ 100.000** *e* status desconhecido há mais de 6 horas (são dois critérios combinados, não um valor isolado).

O FAQ usa um limiar mais baixo (R$ 50 mil vs. R$ 100 mil) e dispensa a condição de tempo. Não necessariamente errado como prática interna de priorização, mas há risco de confusão entre "prioridade alta" (rótulo informal da equipe) e "incidente crítico" (compromisso contratual formal com prazos e penalidades definidos no SLA-2024). Vale confirmar com o cliente se são conceitos intencionalmente distintos ou se o FAQ está desalinhado do critério contratual.

### 2.3. Frete especial — o FAQ já sabe do conflito, mas introduz uma variável nova e não documentada

O item 8 confirma que os atendentes já estão cientes da existência de duas versões do PROC-042 e já adotam a orientação "na dúvida, use a mais recente (v2)" — o que valida na prática a recomendação da Etapa 2.

Porém, o mesmo item acrescenta: *"se o cliente reclamar do valor, pode ser que o contrato dele ainda esteja na tabela antiga."* Essa hipótese de "contratos presos à tabela antiga" **não existe em nenhum dos dois documentos oficiais** — a v2 fala em data de abertura do chamado (regra transitória por data, não por contrato). Isso sugere que pode haver uma prática comercial real (contratos com cláusula de tarifa fixada) não capturada em nenhum documento normativo analisado até aqui, ou é apenas uma suposição do atendente para justificar reclamações. Recomendo perguntar diretamente ao cliente qual das duas hipóteses é verdadeira.

### 2.4. Processo de carga danificada — não encontrado em POL-001

O item 38 descreve um processo específico para carga que chega danificada: janela de **48 horas** para registrar ocorrência com fotos e laudo, encaminhamento ao **Jurídico** (não ao atendimento), e canal próprio (**sinistros@novatech.com.br**).

POL-001 (seção 3.5) menciona avaria em trânsito apenas de passagem, dentro da regra geral de devolução em **7 dias úteis**, sem mencionar a janela de 48h, o e-mail de sinistros, nem o envolvimento do Jurídico. Ou seja: **ou existe um documento próprio de "Política de Sinistros" que não foi incluído nesta amostra, ou esse processo de 48h é uma prática paralela não formalizada.** Nos dois casos, é um risco para o assistente: se ele responder com base apenas em POL-001, vai orientar o fluxo genérico de devolução (7 dias, portal do cliente) em vez do fluxo correto e mais urgente de sinistro (48h, e-mail dedicado).

---

## 3. Gaps de documentação (novos e reforçados)

| Tema | Fonte que menciona | Situação |
|---|---|---|
| Seguro de carga (0,3% / 0,8% do valor declarado) | FAQ item 22 | **Gap total** — nenhum documento oficial sobre o assunto nesta amostra; valores financeiros citados a clientes sem lastro formal |
| Processo de sinistro/carga danificada (48h, Jurídico, e-mail dedicado) | FAQ item 38 | Não coberto por POL-001; possível documento próprio não obtido |
| PROC-088 — Procedimento de Interceptação de Carga (mercadoria em trânsito) | Citado em POL-001, seção 2 | Referenciado, não obtido |
| PROC-043 — Frete de Cargas Perigosas | Já apontado na Etapa 2; agora também citado no FAQ item 32 (frete expresso de carga perigosa) | Reforça prioridade de solicitação ao cliente |

---

## 4. Observação sobre padrão de governança

POL-001 (versão 3.1, responsável Diretoria de Operações, classificação "documento normativo — uso obrigatório") e SLA-2024 (responsável conjunto Comercial + Operações, classificação "documento contratual") têm metadados de governança completos e claros — contraste direto com o PROC-042 (Etapa 2), cujo campo "Status" era ambíguo. Isso sugere que a qualidade de governança documental na NovaTech não é uniforme; pode valer a pena perguntar ao cliente se isso está ligado ao processo de cada área/dono, o que ajudaria a priorizar quais áreas do acervo de ~1.200 documentos merecem checagem de conflito de versão antes de indexar.

---

## 5. Oportunidades

1. **Painel de FAQ certificado**: os itens 3, 15 e 41 já são conhecimento validado — podem virar respostas "oficiais" de alta confiança no assistente antes mesmo de todo o FAQ ser revisado, entregando valor rápido enquanto os outros 44 itens são checados.
2. **Explicações em linguagem natural como complemento às tabelas**: o item 41 traduz a tabela de SLA em linguagem simples e correta — um padrão que pode ser replicado pelo assistente (explicar o "porquê", não só citar a tabela).
3. **Processo de validação do FAQ em escala**: o exercício destas 8 perguntas serviu como piloto. Recomendo aplicar o mesmo método (confirmado / divergente / gap) às 47 perguntas completas antes de qualquer indexação do FAQ na base — o achado do item 45 (regra de desconto incorreta) mostra que isso não é só teórico, já está gerando informação errada.
4. **Hierarquia de confiança nas respostas do assistente**: com um caso real de FAQ divergente da fonte oficial (item 45) e um caso de processo não documentado (item 38), reforça-se a necessidade de o assistente sinalizar sempre a natureza da fonte (normativo/contratual vs. informal) e, quando houver divergência, priorizar o documento oficial mais recente e vigente.
5. **Ligação PROC-042 → POL-001**: a seção 3.5 de POL-001 usa "os mesmos multiplicadores do frete original" para calcular o custo de devolução por desistência — ou seja, resolver a ambiguidade de versão do PROC-042 (Etapa 2) também destrava o cálculo correto de custo de devolução, não é um problema isolado do módulo de frete.

---

## 6. Próximos documentos a solicitar ao cliente

- PROC-043 — Frete de Cargas Perigosas (já sinalizado na Etapa 2, agora reforçado)
- PROC-088 — Procedimento de Interceptação de Carga
- Documentação oficial de seguro de carga (percentuais, elegibilidade, contratos anteriores/posteriores a 2023)
- Documento (se existir) de política de sinistros/carga danificada
- Restante das 47 perguntas do FAQ-Atendimento, para completar a validação

---

*Próxima etapa: aguardando novos artefatos e/ou confirmações do cliente sobre os pontos em aberto.*

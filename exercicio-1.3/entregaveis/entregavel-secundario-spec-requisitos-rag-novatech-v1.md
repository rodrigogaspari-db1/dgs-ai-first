# Especificação de Requisitos de Produto — Pipeline de RAG
## Projeto NovaTech — Assistente de Atendimento (Teams + SharePoint)

> **Registro de revisão — v1.1 (QA):** critérios de teste adicionados ou reforçados em 23 dos 33 requisitos após a revisão de testabilidade conduzida pelo QA. Toda alteração está marcada com 🆕 ao longo do documento e ainda depende de aprovação do Product Specialist. Pontos que dependem de uma decisão de negócio ainda não fechada estão marcados com **[a validar]**. Ver `revisao-qa-testabilidade-rag-novatech.md` para o relatório completo que originou estas mudanças.

**Autor:** Product Specialist, DB1
**Base:** Cenário do projeto, discovery simulado com stakeholders, jornada do atendente (proposta) e documentação da NovaTech (Anexo A — POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento).
**Natureza do documento:** Especificação de produto — não técnica, mas precisa e testável. Define **o que** o pipeline deve garantir; a arquitetura (chunking, embeddings, vector store) é decisão do Tech Lead/Dev, registrada em ADRs separados.

---

## Convenções usadas nesta especificação (formato mínimo verificável)

🆕 Para que os critérios de teste abaixo sejam verificáveis sem depender do desenho final de interface no Teams, esta especificação assume um **formato textual mínimo** que toda resposta do assistente carrega (o design final pode estilizar visualmente — cores, ícones, selos —, mas o conteúdo mínimo abaixo precisa estar presente e ser extraível como texto, inclusive para teste automatizado):

- `Fonte: <tipo, REQ-01> — <nome do documento>, <versão> (vigente desde <data>)` — ou `Fonte: nenhuma encontrada` quando não houver base documental.
- `Confiança: Alta | Média | Baixa` (regra objetiva de cálculo em REQ-08).
- Fonte oriunda de FAQ não-validado: prefixo obrigatório `[NÃO OFICIAL]` antes do conteúdo da resposta.
- Resposta sem fonte documental, originada em conhecimento geral do modelo: prefixo obrigatório `[RESPOSTA GENÉRICA — não baseada em documentação da NovaTech]` (REQ-10).

---

## 1. Fontes de dados: o que é indexado e o que não é

O maior risco deste projeto não é o modelo — é a qualidade e a curadoria dos dados de entrada. A documentação da NovaTech tem documentos oficiais, documentos informais e documentos obsoletos coexistindo sem hierarquia declarada. A pipeline precisa tratar isso como regra de produto, não deixar para o retriever decidir sozinho.

**REQ-01 — Classificação obrigatória de todo documento antes da indexação.**
Todo documento (SharePoint, Confluence, planilhas) recebe, no momento da ingestão, um metadado de **tipo**: `oficial-normativo` (ex.: POL-001), `oficial-contratual` (ex.: SLA-2024), `procedimento-operacional` (ex.: PROC-042), `informal-nao-validado` (ex.: FAQ-Atendimento), ou `obsoleto`.
*Critério de teste:* nenhum documento é indexado sem esse campo preenchido; um relatório de cobertura mostra 100% dos documentos classificados antes do go-live.

**REQ-02 — Documentos sem vigência declarada não entram na base sem tratamento manual prévio.**
Um documento normativo/procedimental sem data de vigência clara (o caso do PROC-042 v1, que nunca foi marcado como obsoleto) não pode ser indexado automaticamente como fonte "ativa" até que o dono da área confirme qual versão vale. Enquanto isso não acontece, o documento entra na base com status `vigência não confirmada`.
*Critério de teste:* dado um par de documentos com o mesmo prefixo/tema e datas de emissão diferentes (ex.: PROC-042 e PROC-042-v2), o pipeline não indexa ambos como "vigentes" sem uma etapa de confirmação humana registrada.

**REQ-03 — Documentos explicitamente superados por uma revisão em andamento são sinalizados, não excluídos.**
O caso da PROC-043 (cargas perigosas, em revisão pelo Compliance) não deve ser simplesmente indexado como fonte definitiva. Documentos referenciados como "em revisão" recebem metadado `sujeito_a_revisao: true` e a resposta do assistente que usar esse documento deve carregar aviso de que o conteúdo pode mudar em breve.
*Critério de teste:* uma pergunta cuja única fonte é um documento com `sujeito_a_revisao: true` retorna resposta com aviso explícito de revisão pendente, não apenas a resposta seca.

**REQ-04 — Conteúdo informal (FAQ) é indexado, mas nunca com o mesmo peso de documento oficial.**
O FAQ-Atendimento tem valor real (linguagem prática, casos reais) mas é explicitamente "não validado por Compliance ou Operações". Ele é indexado com metadado `tipo: informal-nao-validado` e cada item do FAQ, individualmente, pode receber um status de `validado` ou `nao-validado` à medida que o comitê de governança revisar itens específicos (ex.: item 45, que hoje mistura regra da v1 com valores da v2, precisa ser corrigido ou marcado como incorreto antes de qualquer validação).
*Critério de teste 🆕 (reforçado):* uma resposta originada só no FAQ não-validado sempre carrega o prefixo `[NÃO OFICIAL]` (ver Convenções) e nível de confiança Baixa ou Média — nunca Alta; uma resposta equivalente baseada em POL-001 ou SLA-2024 não carrega esse prefixo e pode atingir Alta. Testável comparando o texto literal das duas respostas (ver REQ-08).

**REQ-05 — Fontes fora do escopo documental atual (ex.: PROC-088, política de seguro de carga, política de sinistro formal) não são "inventadas" para preencher lacunas.**
Onde não existe documento oficial (seguro de carga, processo de sinistro fora do POL-001), a pipeline não deve sintetizar uma versão "provável" a partir do FAQ e apresentá-la como se fosse regra normativa. O gap fica explícito (ver Seção 3).
*Critério de teste:* pergunta sobre seguro de carga retorna resposta que identifica a informação como oriunda de FAQ não-validado, sem documento oficial de lastro — nunca como fato normativo citado com a mesma confiança de uma política.

---

## 2. Tratamento de documentos contraditórios

**REQ-06 — Hierarquia de autoridade documental, aplicada por tema, não por documento isolado.**
Ordem de prioridade quando há mais de uma fonte candidata para o mesmo tema:
1. Documento oficial vigente confirmado (com data de vigência validada por um dono de área).
2. Documento oficial candidato, mas sem vigência confirmada (ex.: PROC-042 e PROC-042-v2 hoje, antes de resolução do comitê).
3. Item de FAQ validado pelo comitê de governança.
4. Item de FAQ não validado.
Essa hierarquia é metadado de produto, versionada, não uma instrução solta dentro do prompt.
*Critério de teste 🆕 [novo]:* dado um tema com fontes candidatas em mais de um nível da hierarquia ao mesmo tempo (ex.: procedimento oficial sem vigência confirmada + item de FAQ validado cobrindo o mesmo assunto), a resposta usa exclusivamente a fonte do nível mais alto disponível; se a fonte de nível inferior contradiz a de nível superior, a resposta menciona explicitamente que a divergência foi descartada por hierarquia — nunca ignorada silenciosamente.

**REQ-07 — Quando duas fontes válidas conflitam, o assistente declara o conflito em vez de escolher uma versão silenciosamente.**
Isso cobre exatamente o caso PROC-042 vs PROC-042-v2 (multiplicador regional, fator de peso e prazo adicional divergentes, sem nenhuma marcada como obsoleta no SharePoint). A resposta apresenta as duas versões lado a lado, com data de emissão de cada uma, e reduz o nível de confiança da resposta.
*Critério de teste:* a pergunta "quanto custa o frete especial para 600kg no Sul?" retorna os dois valores calculados (v1 e v2) com suas respectivas fontes e datas — nunca um valor único apresentado como certo.

**REQ-08 — Nível de confiança visível em toda resposta, com regra objetiva de cálculo.**
Três níveis, com critério explícito (não deixado a critério do modelo):
- **Alta:** resposta baseada em documento único, oficial, com vigência confirmada, sem conflito de fonte.
- **Média:** resposta baseada em FAQ validado, ou em documento oficial com vigência não confirmada mas sem versão conflitante conhecida.
- **Baixa:** resposta baseada em fontes conflitantes (REQ-07), em FAQ não validado, ou em documento marcado como `sujeito_a_revisao`.

🆕 **Regra de desempate (adicionada nesta revisão):** um documento `sujeito_a_revisao` (REQ-03) sempre rebaixa a confiança da resposta para, no máximo, Média — mesmo quando não há conflito de versão nem problema de vigência — porque o conteúdo pode mudar em breve. Isso resolve a ambiguidade entre o aviso do REQ-03 e o nível de confiança deste requisito.

*Critério de teste 🆕 (reforçado):* dado o mesmo conjunto de chunks recuperados, dois avaliadores de QA aplicando a regra (incluindo a regra de desempate acima) chegam ao mesmo nível de confiança em pelo menos 10 combinações de teste cobrindo cada nível e o caso de sobreposição com REQ-03.

**REQ-09 — Erros de mistura entre versões (o caso do item 45 do FAQ) são tratados como defeito de dado, não como ambiguidade aceitável.**
O item 45 hoje cita a regra de desconto da v1 ("acima de 10 fretes/mês") mas o valor de desconto correto, segundo a v2 vigente por data, é diferente (a partir de 8 fretes/mês, com faixas de 5%/10%). Esse tipo de contaminação entre versões deve ser detectável em um processo de auditoria periódica do conteúdo indexado (ver REQ-13), não apenas descoberto quando um cliente reclama.
*Critério de teste 🆕 [novo]:* existe um processo de auditoria com frequência definida — proposta do QA: mensal, alinhada à cadência de atualização documental hoje praticada pela NovaTech **[a validar com o cliente]** — que compara documentos correlatos (mesmo prefixo/tema, ex.: PROC-042 e PROC-042-v2) buscando valores divergentes entre trechos indexados; cada execução gera registro (data, documentos comparados, divergências encontradas), revisável por QA.

---

## 3. Resposta fora da base (RAG) — identificação como "genérica"

**REQ-10 — O assistente nunca completa uma lacuna documental com conhecimento geral do modelo sem identificar isso explicitamente.**
Quando a base indexada (Anexo A + demais fontes) não contém informação suficiente para responder com uma fonte rastreável, duas opções são permitidas — e ambas exigem rótulo visível:
- (a) **Recusa explícita:** "Não encontrei essa informação na documentação oficial da NovaTech." — comportamento padrão para perguntas sobre regras de negócio, valores, prazos e políticas.
- (b) **Resposta genérica sinalizada:** permitida apenas para perguntas de conhecimento geral não específico da NovaTech (ex.: explicar o que é um CT-e enquanto conceito de mercado, não uma regra interna) — sempre com rótulo do tipo **"[Resposta genérica — não baseada em documentação da NovaTech, verifique antes de repassar ao cliente]"**.
*Critério de teste 🆕 (reforçado):* nenhuma resposta contendo valor numérico, prazo, percentual ou regra de negócio específica é entregue sem citação de fonte documental; qualquer resposta sem essa citação carrega obrigatoriamente o prefixo `[RESPOSTA GENÉRICA]` (ver Convenções). Regra operacional para decidir a fronteira do caso (b): considera-se "conhecimento geral" apenas conteúdo que não menciona nenhuma entidade específica da NovaTech (nome de documento, valor, prazo, tier, região, cláusula) nem decisão operacional da empresa — qualquer menção a esses elementos exige fonte documental, nunca resposta genérica. **[Regra proposta pelo QA como ponto de partida — confirmar com o cliente, ver ponto em aberto 4, Seção 8, dado o risco jurídico já levantado no discovery.]**

**REQ-11 — O rótulo "genérica" é uma barreira determinística, não apenas uma instrução de prompt.**
Esse comportamento não pode depender só do modelo "lembrar" de se comportar assim — precisa existir uma checagem fora do prompt (um filtro de resposta) que verifica se a resposta final contém pelo menos uma citação de fonte indexada; se não contém, o rótulo de "genérica" é aplicado automaticamente antes de a resposta chegar ao atendente. (Esta é uma decisão de arquitetura que decorre diretamente do requisito de produto — cabe ao Tech Lead formalizar em ADR, mas o requisito de produto é: **toda resposta sem fonte rastreável é rotulada, sem exceção e sem depender só do comportamento do modelo**.)

🆕 **Requisito de suporte (adicionado nesta revisão):** para que o filtro seja verificável, o sistema expõe, para cada resposta, um campo estruturado de citações (lista de fontes usadas) separado do texto livre da resposta — o filtro determinístico verifica esse campo, não o texto da resposta.

*Critério de teste 🆕 [novo]:* rodando um conjunto de perguntas sem correspondência na base (ex.: perguntas de conhecimento geral de mercado), 100% das respostas resultantes carregam o prefixo `[RESPOSTA GENÉRICA]`, verificável a partir do campo estruturado de citações vazio — sem exceção, independentemente da fraseação usada pelo modelo.

**REQ-12 — Casos como o tier "Platinum" (que não existe) são tratados como recusa, nunca como invenção.**
Perguntas sobre entidades, tiers, categorias ou regras inexistentes na base (ex.: "cliente Platinum") devem gerar resposta que declare a inexistência com base na documentação vigente (SLA-2024 define apenas Gold/Silver/Standard), nunca uma resposta fabricada com valores plausíveis.
*Critério de teste (caso-exemplo):* a pergunta "qual o SLA do cliente Platinum?" retorna algo equivalente a "não existe o tier Platinum na documentação vigente (SLA-2024); os tiers existentes são Gold, Silver e Standard", nunca um SLA inventado.

*Critério de teste 🆕 [novo, generalização]:* existe um **dicionário de domínio** — lista fechada e versionada dos valores válidos de: tiers de cliente (Gold/Silver/Standard), regiões de frete (Sul/Sudeste/Centro-Oeste/Nordeste/Norte), classes de carga perigosa (1 a 6, ANTT) e critérios de incidente crítico. Toda pergunta que cite um valor fora desse dicionário (ex.: tier "Platinum", região "Sudoeste") é testada com cobertura mínima de 1 caso por categoria, e deve gerar recusa explícita citando os valores reais — nunca uma resposta fabricada.

---

## 4. Requisitos de atualização e versionamento

**REQ-13 — Prazo máximo de disponibilidade após publicação de novo documento ou nova versão: 24 horas úteis.**
Alinhado à expectativa já validada com o Product Specialist/discovery. Esse prazo cobre a reindexação, não a validação de conteúdo (que segue o fluxo de governança, Seção 6).
*Critério de teste 🆕 (reforçado):* um documento novo publicado no SharePoint/Confluence está pesquisável e citável pelo assistente em até 24h úteis — usando a mesma definição de dia/hora útil do POL-001 (exclui sábados, domingos e feriados nacionais) e a janela de expediente 08h–18h do SLA-2024, seção 5 — com registro de timestamp de ingestão auditável.

**REQ-14 — Toda versão indexada carrega metadado de vigência explícito: data de emissão, data de vigência (se diferente) e status (`vigente`, `revogado`, `vigência não confirmada`, `sujeito a revisão`).**
Nenhum documento é indexado sem esses 4 campos. Documentos que já existem hoje sem essa informação (como PROC-042 e PROC-042-v2) não podem ser corrigidos silenciosamente pelo pipeline — a correção é decisão do dono da área, registrada como mudança de metadado com data e responsável.
*Critério de teste 🆕 [novo]:* uma amostra de 100% dos documentos indexados (incluindo os já existentes hoje, retroativamente) contém os 4 campos preenchidos; documento sem algum desses campos nunca aparece como fonte com nível de confiança Alta (cai automaticamente para Baixa, por REQ-08).

**REQ-15 — Processo de revisão periódica obrigatório, não apenas ingestão reativa.**
Cada fonte tem um dono definido (ver mapeamento no discovery: Operações+TI para frete, Comercial para devolução/SLA, Jurídico+Compliance para seguro/sinistro, Atendimento+Compliance para o FAQ, TI para estrutura documental) e um SLA de revisão. Sem isso, o problema do PROC-042 se repete em escala à medida que o acervo completo (~1.250 fontes) é indexado.
*Critério de teste 🆕 (reforçado):* existe um relatório periódico (ex.: mensal) listando documentos sem revisão dentro do SLA definido pelo dono da fonte. **[O valor do SLA de revisão por fonte ainda não está definido — ver ponto em aberto 3, Seção 8. Até essa definição, o requisito é testável apenas quanto à existência do relatório, não quanto ao cumprimento do prazo.]**

**REQ-16 — Documentos revogados ou substituídos permanecem no histórico para auditoria, mas saem do conjunto de recuperação ativa.**
Isso evita tanto o problema atual (v1 nunca arquivada, retornando lado a lado com a v2 sem hierarquia) quanto a perda de rastreabilidade histórica exigida pelo Jurídico/Compliance.
*Critério de teste 🆕 [novo]:* uma pergunta cuja única fonte seria um documento com status `revogado` não retorna esse documento no conjunto de retrieval ativo (a resposta usa a versão vigente, conforme REQ-06, ou declara ausência de fonte vigente); o mesmo documento revogado permanece consultável em uma trilha de auditoria/histórico separada, acessível a perfis autorizados (REQ-26).

---

## 5. Rastreabilidade da fonte

**REQ-17 — Toda resposta com base documental cita: nome do documento, versão, seção/trecho específico e data de vigência.**
Não basta citar "PROC-042" — precisa distinguir explicitamente v1 de v2, com a data. Isso é o que teria evitado a ambiguidade que hoje o atendente resolve "perguntando para quem sabe".
*Critério de teste 🆕 [novo — prioridade alta]:* para cada resposta amostrada com base documental, verifica-se a presença dos 4 elementos obrigatórios (nome do documento, versão, seção/trecho específico, data de vigência); a ausência de qualquer um dos 4 reprova o caso de teste. Cobertura mínima: 100% das perguntas do mapa de cobertura do Anexo B.

**REQ-18 — O atendente pode visualizar o trecho original citado sem sair do fluxo de atendimento (um clique, sem abrir o SharePoint).**
Já presente na proposta de jornada; é requisito de produto porque sem isso a citação de fonte vira apenas um selo decorativo, não uma forma real de verificação pelo atendente.
*Critério de teste 🆕 [novo]:* em teste de usabilidade, o número de cliques/toques necessários para visualizar o trecho original a partir da resposta no Teams é igual a 1, sem sair da conversa ativa com o cliente.

**REQ-19 — Toda interação gera registro de auditoria: pergunta, resposta, fonte(s) citada(s), nível de confiança e se houve sinalização de erro.**
Esse registro é o que Jurídico e Compliance apontaram como ausente hoje e necessário para qualquer defesa em caso de disputa contratual (ex.: o caso do cliente com 8-9 fretes/mês informado incorretamente sobre desconto).
*Critério de teste 🆕 [novo — prioridade alta]:* uma amostra de 100% das interações do período de teste gera registro de auditoria com os 5 campos obrigatórios (pergunta, resposta, fonte(s) citada(s), nível de confiança, indicação de sinalização de erro); interação sem registro completo reprova o caso de teste.

**REQ-20 — O canal de sinalização de erro (feedback do atendente) está ligado à mesma trilha de rastreabilidade — uma sinalização registra qual resposta, qual fonte e qual atendente identificou o problema, e atualiza o status de confiança daquele trecho para outros atendentes até resolução.**
*Critério de teste 🆕 [novo]:* ao sinalizar uma resposta como incorreta, o registro de auditoria da interação original é atualizado/vinculado com a sinalização, o motivo e o atendente responsável; o status de confiança do trecho correspondente muda para refletir a sinalização pendente, verificável consultando o mesmo trecho a partir de outro atendente antes da resolução.

---

## 6. Requisitos de governança que sustentam a pipeline (pré-condição, não "nice to have")

Sem isso, os requisitos acima não se sustentam em escala:

- Comitê de governança documental com donos por fonte e SLA de revisão definido (REQ-15).
- Processo formal para resolver, antes do go-live, os casos já identificados: qual versão do PROC-042 é vigente, formalização (ou não) do processo de sinistro, confirmação (ou não) da regra de seguro de carga, prazo de conclusão da revisão da PROC-043.
- Sem resolução desses pontos específicos, a pipeline pode e deve ir ao ar tratando-os como "vigência não confirmada" / "sem lastro oficial" (conforme Seções 1-3) — não é bloqueador de go-live, é um requisito de como o produto se comporta na ausência de resolução.

---

## 7. Fora de escopo desta especificação (decisão técnica, não de produto)

- Estratégia de chunking, modelo de embeddings, parâmetros de indexação vetorial (tipo de índice, dimensionalidade, estratégia de escala) — tratados em ADR pelo Tech Lead. O motor de armazenamento vetorial em si já é decisão de negócio (ver Seção 9 — PostgreSQL/pgvector) e não está mais em aberto para essa escolha específica.
- Implementação do filtro determinístico citado no REQ-11 — a exigência de produto é o comportamento observável (toda resposta sem fonte é rotulada), a implementação é do time técnico.
- Implementação técnica do controle de acesso por perfil (Seção 10) — integração com Azure AD/SharePoint, modelo de permissões — é decisão do Tech Lead; a exigência de produto é o comportamento observável (REQ-25 a REQ-29).

---

## 8. Pontos em aberto para validar no discovery real com o cliente

1. Confirmar com Operações/Comercial qual versão do PROC-042 é de fato vigente hoje (não pode ser decisão do pipeline).
2. Confirmar prazo de conclusão da revisão da PROC-043 e se ela deve ser indexada antes ou depois da conclusão.
3. Validar limiares exatos de confiança (Alta/Média/Baixa) com os donos de cada fonte.
4. Definir, com o cliente, se a resposta "genérica" (REQ-10b) é permitida no produto ou se o assistente deve *sempre* se recusar fora da base documental (opção mais conservadora, a considerar dado o risco jurídico já levantado por Thiago Bezerra no discovery).
5. Confirmar o SLA de resolução das sinalizações de erro por fonte, antes do piloto.
6. Classificar, junto aos donos de cada área, a confidencialidade (Confidencial/Interno/Público) dos 5 documentos do Anexo A e do restante do acervo, antes do go-live (Seção 10).
7. Mapear quais perfis/grupos já existentes no Azure AD/SharePoint correspondem a "atendente padrão", "supervisor", "jurídico", "comercial sênior" etc., para viabilizar o controle de acesso por perfil (REQ-26).
8. Confirmar quem assume a responsabilidade de manutenção e backup do banco PostgreSQL/pgvector (DB1 ou TI da NovaTech) e validar o teto de custo mensal de infraestrutura antes do piloto (Seção 9).
9. 🆕 Confirmar frequência e método do processo de auditoria periódica de contradições entre documentos correlatos (REQ-09) — proposta do QA: mensal, a validar.
10. 🆕 Definir os valores exatos de RTO (tempo máximo de indisponibilidade) e RPO (perda máxima de dados aceitável) para o restore do banco vetorial (REQ-24), a validar com TI/Tech Lead.
11. 🆕 Confirmar o limiar de similaridade e o percentual mínimo de acerto por ramo hierárquico que disparam expansão da busca para a base inteira (REQ-32), a validar com o Tech Lead.

---

## 9. Requisito não funcional — armazenamento vetorial em PostgreSQL/pgvector

Decisão de negócio: o mecanismo de armazenamento e busca vetorial da base de conhecimento é **PostgreSQL com a extensão pgvector**, em vez de um serviço de busca vetorial gerenciado dedicado. O racional é custo previsível e reaproveitamento de operação/expertise já existente em Postgres, em vez de somar um serviço proprietário adicional à conta da Azure. Essa é uma decisão de produto/negócio que orienta a arquitetura — os parâmetros técnicos de implementação continuam sendo do Tech Lead.

**REQ-21 — O motor de armazenamento vetorial da base de conhecimento é PostgreSQL/pgvector.**
Essa decisão substitui, como opção de arquitetura, a alternativa de um serviço de busca vetorial gerenciado dedicado (ex.: Azure AI Search como camada vetorial) considerada anteriormente em discussão de arquitetura (ADR-0004, Tech Lead). A escolha do motor já está definida por decisão de negócio; cabe ao Tech Lead formalizar os parâmetros técnicos (tipo de índice, dimensionalidade dos embeddings, estratégia de escala horizontal) em ADR atualizado.
*Critério de teste:* o ADR de build vs. buy do pipeline de RAG referencia esta decisão como premissa fechada, não como opção em aberto.

**REQ-22 — O custo de armazenamento e operação do banco vetorial é rastreado como item de custo recorrente do projeto, com teto definido antes do go-live.**
Esse é o racional de negócio por trás da escolha (evitar custo variável de um serviço de busca gerenciado cobrado por volume de índice/consultas).
*Critério de teste:* existe uma estimativa de custo mensal de infraestrutura (compute + storage do Postgres) validada antes do piloto, e um processo de acompanhamento mensal desse custo frente ao teto definido.

**REQ-23 — A responsabilidade de manutenção (backup, disponibilidade, atualização de versão do Postgres/pgvector, monitoramento) é definida e atribuída a uma área (DB1 ou TI da NovaTech) antes do go-live.**
Como não é um serviço gerenciado dedicado à busca vetorial, a manutenção operacional recai sobre quem já opera o Postgres do ambiente. Isso precisa constar explicitamente, com SLA de disponibilidade compatível com os compromissos contratuais já assumidos com o cliente em SLA-2024 — indisponibilidade do banco vetorial não pode ser a causa de descumprimento de SLA de atendimento (ex.: resposta em até 2h para cliente Gold).
*Critério de teste 🆕 [novo]:* existe documento formal nomeando a área responsável pela manutenção do banco vetorial, com meta de disponibilidade mínima explícita — recomenda-se herdar o piso já contratual do SLA-2024 (99,5% para compromissos equivalentes ao tier Gold) — medida mensalmente e comparada à meta definida.

**REQ-24 — Existe plano de recuperação de desastre (backup/restore) testado para o banco vetorial, por ele armazenar a base de conhecimento operacional crítica do atendimento.**
Perda ou corrupção da base vetorial não pode significar reindexação manual do zero sob pressão, com o time de atendimento sem o assistente disponível. O processo de restore é testado antes do go-live, não apenas documentado em teoria.
*Critério de teste 🆕 (reforçado):* um teste de restore é executado em ambiente de homologação antes do go-live, com RTO (tempo máximo de indisponibilidade) e RPO (perda máxima de dados) definidos e medidos — **[valores exatos de RTO/RPO a validar com o Tech Lead/TI antes do piloto, ver ponto em aberto 10, Seção 8]** — e o resultado (tempo real de restore, integridade dos dados restaurados) é documentado e aprovado antes do go-live.

---

## 10. Camada de governança de dados e controle de acesso por perfil

Requisito de produto motivado pelo próprio conteúdo da documentação: ela mistura material normativo, contratual e informal, e parte desse conteúdo (condições comerciais negociadas por cliente, processos jurídicos em andamento) pode exigir restrição de acesso mesmo dentro do time de atendimento — não é seguro assumir que todo atendente deve enxergar tudo que está indexado.

**REQ-25 — Todo documento e, por herança, todo chunk gerado a partir dele, recebe uma classificação de confidencialidade: Confidencial, Interno ou Público, além da classificação de tipo já prevista no REQ-01.**
Essa classificação é definida pelo dono da fonte (mesmo mapeamento de donos da Seção 6) no momento da ingestão — não é inferida automaticamente pelo pipeline. Uma seção específica de um documento majoritariamente Interno pode ser marcada como Confidencial isoladamente (ex.: uma cláusula de desconto negociado dentro de um procedimento geral), sobrepondo a classificação padrão do documento.
*Critério de teste:* nenhum documento é indexado sem esse metadado; um relatório de cobertura mostra 100% dos documentos e chunks classificados antes do go-live.

**REQ-26 — O acesso a chunks é filtrado pelo perfil do usuário antes da geração da resposta, não depois.**
Um atendente padrão acessa chunks classificados como Interno e Público; conteúdo Confidencial (ex.: condições contratuais específicas de um cliente, detalhes de processo jurídico em andamento) só é recuperável para perfis autorizados (ex.: supervisor, jurídico, comercial sênior). A integração deve aproveitar os grupos/perfis de acesso já existentes no Azure AD/SharePoint (ambiente Microsoft já em uso pela NovaTech), em vez de criar um sistema de permissão paralelo.
*Critério de teste 🆕 (reforçado):* a mesma pergunta, feita por um atendente padrão e por um supervisor autorizado, pode gerar respostas diferentes em completude — mas o atendente padrão nunca recebe conteúdo Confidencial fora do seu perfil. **[Cobertura completa depende do mapeamento de todos os perfis/grupos — ver ponto em aberto 7, Seção 8; até lá, o teste cobre pelo menos os 2 perfis já exemplificados.]**

**REQ-27 — Quando a única fonte disponível para responder é Confidencial e o usuário não tem permissão de acesso, o assistente não revela a existência do conteúdo restrito — comporta-se como se a informação não estivesse na base (mesmo comportamento do REQ-10a).**
Isso evita o vazamento indireto de "existe algo, mas você não pode ver", que por si só já é informação sensível em alguns contextos (ex.: confirmar que existe um processo jurídico em andamento sobre um cliente específico).
*Critério de teste 🆕 [novo]:* compara-se a resposta (texto e tempo de resposta) de uma pergunta cuja única fonte é Confidencial e não autorizada para o perfil do usuário com a resposta de uma pergunta genuinamente sem informação na base; as duas devem ser indistinguíveis em conteúdo e sem diferença de latência perceptível que permita inferir a existência do conteúdo restrito.

**REQ-28 — Toda decisão de acesso negado é registrada na trilha de auditoria (REQ-19), com identificação do usuário, do chunk restrito e do motivo da restrição.**
Isso permite ao Compliance revisar periodicamente se as classificações estão adequadas e identificar padrões de tentativa de acesso a conteúdo restrito que sugiram necessidade de revisão de perfil ou de reclassificação.
*Critério de teste 🆕 [novo]:* uma amostra de 100% das tentativas de acesso negado durante o período de teste gera entrada na trilha de auditoria com usuário, chunk restrito e motivo; tentativa sem registro reprova o caso de teste.

**REQ-29 — A reclassificação de confidencialidade de um documento ou chunk (ex.: conteúdo que era Confidencial passa a Interno após aprovação) segue o mesmo processo de governança da Seção 6 — é decisão do dono da fonte, com data e responsável registrados, nunca uma mudança automática do pipeline.**
*Critério de teste 🆕 [novo]:* toda mudança de classificação de confidencialidade é registrada com data, responsável e justificativa; não existe caminho no sistema que permita alterar a classificação sem esse registro — verificável tentando reclassificar sem preencher os campos obrigatórios e confirmando que a ação é bloqueada ou fica pendente de aprovação.

---

## 11. Camada de gestão semântica hierárquica dos chunks

Requisito de produto que decorre da forma como a documentação da NovaTech já existe hoje: organizada por área responsável (Operações, Compliance, Comercial) e por pastas temáticas dentro do SharePoint/Confluence. Aproveitar essa hierarquia física no processo de carga acelera a atualização, reduz custo de reprocessamento e melhora a qualidade da recuperação — em vez de tratar o acervo como uma massa plana de documentos.

**REQ-30 — O processo de ingestão preserva a hierarquia física de origem do documento (área responsável > pasta temática > documento > seção) como metadado estrutural de cada chunk, não apenas como referência de exibição.**
Cada chunk carrega, além do conteúdo, seu caminho de origem completo (ex.: Comercial > Frete > PROC-042-v2 > Seção 2.1). Isso é a base sobre a qual os requisitos seguintes se apoiam.
*Critério de teste 🆕 [novo]:* uma amostra de 100% dos chunks armazenados contém os 4 níveis de metadado hierárquico preenchidos (área > pasta > documento > seção); chunk sem essa cadeia completa reprova o caso de teste.

**REQ-31 — Atualizações de documentação disparam reprocessamento de embedding escopado à hierarquia afetada, não à base inteira.**
Se apenas a pasta de "Frete" do Comercial é atualizada em um mês, apenas os documentos e chunks daquele ramo são reprocessados — reduzindo tempo e custo de reembedding e viabilizando, na prática e em escala, o prazo de 24h úteis do REQ-13 mesmo quando o acervo crescer para as ~1.250 fontes completas do ambiente NovaTech.
*Critério de teste:* uma atualização isolada em um documento de uma pasta específica não gera reprocessamento de embeddings de documentos de áreas/pastas não alteradas — verificável por log de reprocessamento por ramo.

**REQ-32 — A hierarquia física também funciona como filtro de recuperação (retrieval), restringindo a busca vetorial a um ramo específico quando o contexto da pergunta já indica o tema.**
Ex.: uma pergunta sobre frete busca prioritariamente dentro do ramo Comercial > Frete antes de abrir a busca para a base inteira. Isso reduz o risco de um chunk de área/tema não relacionado "vazar" para o topo do ranking de similaridade — mitigação adicional ao problema de contaminação entre documentos de temas próximos (ver REQ-09).
*Critério de teste 🆕 [novo]:* para um conjunto de perguntas de teste com tema conhecido (mapa de cobertura do Anexo B), pelo menos 80% dos chunks recuperados no top-5 pertencem ao ramo hierárquico correto antes de qualquer expansão da busca; a expansão para a base inteira só ocorre quando a similaridade dos resultados dentro do ramo fica abaixo de um limiar definido. **[Limiar exato de expansão e o percentual mínimo de 80% são propostas do QA como ponto de partida — validar com o Tech Lead, ver ponto em aberto 11, Seção 8.]**

**REQ-33 — A hierarquia física alimenta automaticamente o mapeamento de donos por fonte (Seção 6 / REQ-15): ao subir um nível na hierarquia (pasta/área), o pipeline já associa o dono responsável por aquele ramo, sem necessidade de cadastro manual por documento individual.**
Isso reduz o atrito operacional de manter o comitê de governança atualizado à medida que novos documentos entram em pastas já mapeadas, e reforça a REQ-25 (classificação de confidencialidade também pode herdar um padrão por pasta/área, com exceções pontuais por documento ou seção).
*Critério de teste 🆕 [novo]:* ao publicar um novo documento em uma pasta/área já mapeada no comitê de governança, o dono responsável é atribuído automaticamente ao registro do documento, sem cadastro manual — verificável comparando o dono atribuído ao mapeamento oficial da Seção 6.

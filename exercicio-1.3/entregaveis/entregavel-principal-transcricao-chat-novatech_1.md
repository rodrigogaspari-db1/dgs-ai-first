# Transcrição da Conversa — Projeto NovaTech (Product Specialist + QA)

**Participantes:** Rodrigo (usuário, papel Product Specialist/QA no exercício) e Claude (Cowork)
**Ferramenta:** Claude (Cowork)
**Contexto:** Sessão de trabalho cobrindo o entendimento do cenário NovaTech, a especificação de requisitos de produto para o pipeline de RAG e a revisão de testabilidade desses requisitos como QA.

---

## Turno 1

**Usuário:**

> Preciso que avalie como um Product Specialist o cenário do projeto em anexo: "exercicio-fase-1-entendimento.md" (seção cenário e informações adicionais fornecidas pela novatech), avalie também o discovery realizado em fases anteriores do projeto (discovery-simulado-stakeholders-novatech.md, jornada-atendente-diagrama-novatech.html, jornada-atendente-proposta-novatech.md) e também a documentação da novatech que apresenta contradições ("anexo-a-documentacao-simulada-novatech.md", "POL-001-politica-devolucao.md", "PROC-042-frete-especial-v1.md", "PROC-042-v2-frete-especial-revisado.md", "SLA-2024-tabela-sla-clientes.md", "FAQ-atendimento.md"), por enquanto apenas carregue as informações do projeto para que em seguida possamos discutir alguns requisitos do produto
>
> *(9 arquivos anexados: exercicio-fase-1-entendimento.md, discovery-simulado-stakeholders-novatech.md, jornada-atendente-diagrama-novatech.html, jornada-atendente-proposta-novatech.md, anexo-a-documentacao-simulada-novatech.md, FAQ-atendimento.md, POL-001-politica-devolucao.md, PROC-042-frete-especial-v1.md, PROC-042-v2-frete-especial-revisado.md, SLA-2024-tabela-sla-clientes.md)*

**Claude:**

> Pronto — revisei todos os 9 documentos como Product Specialist. Aqui está o que carreguei e o que já salta aos olhos antes de entrarmos nos requisitos:
>
> **Cenário e contexto do projeto**
> NovaTech (logística, 1.200 funcionários) quer um assistente de IA integrado a Teams+SharePoint para reduzir o tempo de busca de informação de 12 min para <2 min por chamado (320 chamados/dia, 60% envolvem consulta a documentação, 45 atendentes). Documentação espalhada em SharePoint (~800 docs), Confluence (~400 páginas) e planilhas de rede (~50), atualizada por 3 áreas sem processo unificado de revisão. Orçamento: 3 meses (discovery + dev + go-live).
>
> **Discovery já realizado (simulado)**
> A sessão com stakeholders (Fernanda/Atendimento, Marcos/TI, Juliana/Compliance, Rafael/Comercial, Débora/Operações, Thiago/Jurídico) confirma e nomeia donos para os problemas técnicos já achados: 4,2 fontes consultadas por chamado, 19% de escalonamento (~30% em frete), nota de satisfação 6,1/10. O ponto central que emerge: o problema não é falta de conteúdo, é falta de fonte única com dono e vigência claros, mais um canal formal de sinalização de erro — hoje isso vira mensagem avulsa no Teams sem dono nem rastro.
>
> **Jornada do atendente (proposta + diagrama)**
> Já modelada em 3 trilhas: fluxo principal (pergunta → resposta com fonte, versão e nível de confiança → uso → encerramento com auditoria), fallback (conflito de fontes ou ausência de base → declara divergência, nunca decide sozinho, roteia ao dono/supervisor com contexto anexado) e feedback (botão de sinalização → chamado ao dono da fonte com SLA → aviso visível no conteúdo até resolução). Os guardrails já propostos: nunca inventar informação, sempre citar fonte+vigência, declarar conflito em vez de escolher versão silenciosamente, selo de "não oficial" para conteúdo não validado, não avançar sobre juízo jurídico/comercial sem lastro documental.
>
> **Documentação da NovaTech — contradições e gaps confirmados**
> - PROC-042 (v1, mar/2023) vs PROC-042-v2 (nov/2023): multiplicadores regionais diferentes, fator de peso diferente (1.0/1.2/1.5 vs 1.0/1.15/1.4), prazo adicional diferente (+2 vs +3 dias). Nenhum dos dois está marcado como obsoleto; a "disposição transitória" da v2 já venceu (dez/2023) sem arquivamento do v1.
> - FAQ item 45 mistura regras: cita desconto "acima de 10 fretes/mês" (regra da v1), enquanto a v2 vigente diz "a partir de 8 fretes/mês" com faixas de 5%/10%. Isso é o erro real que pode sair como resposta "com fonte" errada.
> - FAQ item 15 (tier "Platinum" não existe) é um teste clássico de alucinação — o SLA-2024 só define Gold/Silver/Standard.
> - FAQ item 3 e item 22 trazem prática/regra (exceção para carga perigosa via Gestão de Riscos; seguro de carga 0,3%/0,8%) que não têm nenhum lastro em documento oficial — risco jurídico se o assistente citar isso como se fosse política formal.
> - Gap: processo de sinistro/carga danificada (FAQ item 38) existe na prática mas não está formalizado no POL-001 — se o assistente só usar documentação oficial, vai negar um processo que na prática existe.
> - PROC-043 (cargas perigosas) está em revisão pelo Compliance — conteúdo pode nascer desatualizado se indexado antes da conclusão.
>
> Tenho tudo isso mapeado e pronto para cruzar quando você quiser discutir requisitos — seja hierarquia de fontes, critérios de confiança, versionamento/vigência, tratamento do FAQ não validado, ou qualquer outro ponto específico. Como quer seguir?

---

## Turno 2

**Usuário:**

> agora baseado no cenário desse projeto precisamos mapear uma boa estratégia de Pipeline de RAG (Retrieval-Augmented Generation), para o projeto, levando em conta os documentos que precisam ser indexados, tratativa para contradições, quando a resposta não vier da RAG (mas de modelos gerais) essa informação deverá ser identificada como "genérica" ou algo semelhante, requisitos de atualização dos documentos e/ou versionamento e rastreabilidade da fonte.
>
> levando em consideração essas especificidades dos dados do projeto gere uma lista de especificações para a pipeline de RAG

**Claude:**

*(Gerou e entregou o arquivo `spec-requisitos-rag-novatech.md`, v1.0, com 20 requisitos — REQ-01 a REQ-20 — organizados em: fontes de dados a indexar, tratamento de contradições, resposta fora da base identificada como "genérica", atualização/versionamento e rastreabilidade da fonte.)*

> Preparei a especificação cobrindo os 5 pontos que você pediu, sempre ancorada em casos concretos da documentação da NovaTech (não genéricos):
>
> - **Fontes a indexar** — classificação obrigatória por tipo (oficial-normativo, contratual, procedimento, informal-não-validado), com regra explícita para o caso PROC-042/PROC-042-v2 (não indexar como "vigente" sem confirmação humana) e para a PROC-043 em revisão.
> - **Contradições** — hierarquia de autoridade documental (oficial vigente > oficial sem vigência confirmada > FAQ validado > FAQ não validado), regra de "declarar o conflito, nunca escolher em silêncio" (caso PROC-042), e critério objetivo de nível de confiança (Alta/Média/Baixa) testável por dois QAs independentes.
> - **Resposta fora da base → "genérica"** — recusa explícita como padrão para regras de negócio, e um rótulo obrigatório tipo "[Resposta genérica — não baseada em documentação da NovaTech]" quando o modelo responder com conhecimento geral. Frisei que isso precisa de uma checagem determinística fora do prompt, não só uma instrução — decisão que caberá ao Tech Lead formalizar em ADR, mas o requisito de produto é o comportamento observável.
> - **Atualização/versionamento** — prazo de 24h úteis para disponibilidade de novo documento, 4 metadados obrigatórios de vigência, processo de revisão periódica com donos por fonte (usando o mapeamento que já saiu do discovery).
> - **Rastreabilidade** — citação sempre com documento + versão + trecho + data de vigência, visualização em um clique, e registro de auditoria ligando pergunta → resposta → fonte → sinalização.
>
> Também deixei uma seção de pontos em aberto (ex.: se a "resposta genérica" deve realmente ser permitida ou se o produto deve sempre se recusar fora da base — dado o risco jurídico que o Thiago Bezerra já levantou no discovery).
>
> Quer que a gente refine algum requisito específico, ou parta agora para o próximo passo — por exemplo, transformar isso em ADRs técnicos ou em critérios de aceitação para o QA testar?

---

## Turno 3

**Usuário:**

> vamos revisar e incrementar alguns requisitos baseado em decisões de negócio, aqui vão alguns itens a considerar:
>
> * considere como um requisito não funcional a utilização de um banco vetorial para armazenamento vetorial dos dados em um banco pgvector (Postgres) relacionados ao custo e manutenção.
> * considere como requisito uma camada de governança de dados que os classifique como confidencial, interno e publico que faça com que o acesso a chuncks gerados por eles sejam também avaliados por nível de perfil de usuários.
> * considere a criação de uma camada de gestão semantical para os chunks, aonde o processo de carregamento levem em consideração a hierarquia física dos documentos e isso acelere o processo de embedding dos chunks

**Claude:**

*(Adicionou ao `spec-requisitos-rag-novatech.md` três novas seções — 9, 10 e 11 — com os requisitos REQ-21 a REQ-33, e reenviou o arquivo atualizado.)*

> Incorporei os três pontos como novas seções (9, 10 e 11), mantendo a numeração anterior (REQ-01 a REQ-20) intacta e adicionando REQ-21 a REQ-33:
>
> **Seção 9 — NFR PostgreSQL/pgvector:** registrei a decisão como premissa fechada (não mais opção em aberto no ADR-0004 do Tech Lead), e desdobrei em requisitos testáveis: custo mensal com teto definido (REQ-22), dono da manutenção/backup com SLA compatível com os compromissos de SLA-2024 (REQ-23), e plano de disaster recovery testado antes do go-live (REQ-24) — já que essa base vetorial vira infraestrutura crítica de atendimento.
>
> **Seção 10 — Governança de acesso por perfil:** classificação obrigatória de confidencialidade (Confidencial/Interno/Público) por documento e por chunk, com possibilidade de uma seção específica ter classificação diferente do documento todo (ex.: uma cláusula de desconto negociado dentro de um procedimento geral). O filtro de acesso acontece antes da geração da resposta, aproveitando os grupos já existentes no Azure AD/SharePoint. Importante: quando o usuário não tem permissão, o assistente se comporta como se a informação simplesmente não existisse (REQ-27) — evita vazar a informação de que "existe algo restrito".
>
> **Seção 11 — Gestão semântica hierárquica:** a ingestão preserva o caminho físico de origem (área > pasta temática > documento > seção) como metadado do chunk. Isso alimenta três coisas ao mesmo tempo: reprocessamento de embedding escopado só ao ramo alterado (acelera e barateia a atualização, sustentando o SLA de 24h mesmo quando o acervo crescer para as ~1.250 fontes), filtro de retrieval por tema (reduz contaminação entre documentos de áreas diferentes) e associação automática do dono de governança por ramo, sem cadastro manual por documento.
>
> Também atualizei a seção "Fora de escopo" (o motor vetorial já não está em aberto, só os parâmetros técnicos) e adicionei 3 novos pontos à lista de "pontos em aberto para validar no discovery real" — classificação de confidencialidade dos documentos existentes, mapeamento dos perfis do Azure AD, e definição de quem assume manutenção/custo do Postgres.
>
> Quer que a gente refine algum desses pontos em aberto agora, ou parte para o próximo artefato — por exemplo, transformar isso em ADRs técnicos com o Tech Lead?

---

## Turno 4

**Usuário:**

> agora como uma analista de garantia de qualidade (QA - Quality Assurance) revise os requisitos e veja se a especificação deles está "testável", ou seja é possível ser observada e validada quando o produto final for entregue.

**Claude:**

*(Gerou e entregou o arquivo `revisao-qa-testabilidade-rag-novatech.md`, com veredito individual para os 33 requisitos, notas de ambiguidade e recomendação priorizada.)*

> O achado principal: **17 dos 33 requisitos (~52%) não têm uma frase explícita de "critério de teste"** — descrevem bem a intenção, mas terminam na explicação de racional, sem uma linha objetiva de "como o QA verifica isso no produto entregue". Os dois casos mais graves são REQ-17 (citação de fonte) e REQ-19 (log de auditoria) — justamente os dois pilares que Jurídico e Compliance pediram no discovery, e nenhum tem verificação objetiva hoje.
>
> Além disso, apontei ambiguidades específicas em 6 requisitos que têm critério mas dependem de algo ainda indefinido: por exemplo, REQ-04/REQ-08 citam "selo visual" sem nenhuma referência de UI; REQ-10/REQ-11 (o requisito de "resposta genérica" que você pediu para adicionar) tem fronteira subjetiva entre "conhecimento geral" e "regra específica"; REQ-23/REQ-24 falam em disponibilidade "compatível" e restore "testado" sem números de SLA/RTO/RPO; e REQ-32 não define o limiar que dispara a expansão de busca fora do ramo hierárquico.
>
> Também separei os requisitos por tipo de verificação (teste de sistema automatizável vs. auditoria de processo organizacional vs. teste de infraestrutura), porque a estratégia de teste é diferente para cada grupo.
>
> Fechei com uma recomendação priorizada — corrigir REQ-17/REQ-19 primeiro, depois decidir a fronteira da resposta genérica, depois quantificar os limiares soltos. Posso aplicar essas correções diretamente na especificação principal agora, ou prefere fechar antes as decisões de negócio pendentes (fronteira da resposta genérica, SLAs) para consolidarmos a versão testável de uma vez?

---

## Turno 5

**Usuário:**

> edite então os requisitos que ainda não estão "testáveis" e destaque os critérios que adicionar para que eu revise e aprove

**Claude:**

*(Editou o `spec-requisitos-rag-novatech.md`, adicionando ou reforçando o critério de teste em 23 dos 33 requisitos, todos marcados com 🆕 para revisão, incluindo uma nova seção de "Convenções" para tornar verificáveis os requisitos que dependiam de um formato de UI ainda não definido. Reenviou o arquivo.)*

> Editei os 23 requisitos apontados como não-testáveis ou parcialmente testáveis na revisão de QA. Tudo que mudou está marcado com 🆕 para você revisar rapidamente — aqui está o resumo do que fiz, por tipo de intervenção:
>
> **Critério de teste adicionado do zero** (16 requisitos que não tinham nenhum): REQ-06, 09, 11, 14, 16, 17, 18, 19, 20, 23, 27, 28, 29, 30, 32, 33.
>
> **Critério existente reforçado** (7 requisitos que tinham critério, mas ambíguo): REQ-04, 08, 10, 13, 15, 24, 26.
>
> **Três decisões de conteúdo que precisei tomar para tornar os critérios verificáveis** — todas marcadas e justificadas no texto, para você aprovar ou ajustar:
>
> 1. Criei uma seção nova de **"Convenções"** logo no início do documento, definindo um formato textual mínimo (`Fonte:`, `Confiança:`, prefixos `[NÃO OFICIAL]` e `[RESPOSTA GENÉRICA]`) — sem isso, requisitos como REQ-04 e REQ-10 não tinham como ser verificados objetivamente, já que citavam "selo visual" sem nenhuma referência de formato.
> 2. No REQ-08, adicionei uma **regra de desempate** explícita: documento `sujeito_a_revisao` sempre rebaixa a confiança para no máximo Média — isso resolve a sobreposição ambígua com o REQ-03 que a revisão de QA tinha apontado.
> 3. No REQ-10, propus uma **regra operacional** para separar "conhecimento geral" de "regra específica da NovaTech" (qualquer menção a documento, valor, prazo, tier, região ou cláusula exige fonte) — deixei explícito que essa é uma proposta do QA como ponto de partida, não uma decisão fechada, porque o risco jurídico levantado por Thiago Bezerra no discovery ainda pede validação do cliente.
>
> Onde o critério depende de um número ainda não definido (SLA de revisão, RTO/RPO do restore, limiar de expansão de busca por ramo, frequência da auditoria de contradições), marquei com **[a validar]** em vez de inventar um valor — e adicionei 3 novos itens (9, 10, 11) à lista de pontos em aberto da Seção 8 para não perder o rastro dessas pendências.
>
> Fico no aguardo da sua revisão — pode aprovar como está, pedir ajuste em algum critério específico, ou já entrarmos nas decisões de negócio pendentes (a regra de "resposta genérica" é provavelmente a mais sensível, dado o risco jurídico).

---

## Turno 6

**Usuário:**

> agora gere a versão final do spec requisitos rag novatech considerando todas os complementos de verificação

**Claude:**

*(Consolidou o `spec-requisitos-rag-novatech.md` na versão final v2.0 — removendo as marcações de revisão (🆕) e organizando o conteúdo como especificação única, mantendo apenas as marcações **[a validar]** para decisões de negócio genuinamente pendentes. Adicionou uma Seção 12 de sumário de rastreabilidade. Reenviou o arquivo final.)*

> Consolidei a versão final (v2.0). O que mudou em relação à versão de revisão:
>
> - Removi todas as marcações de trabalho (🆕, "reforçado", "[novo]") e deixei o texto correndo como uma especificação única e coerente — não mais um "diff" de revisão.
> - Mantive apenas os marcadores **[a validar]**, porque esses não são resíduo de revisão: são decisões de negócio reais ainda em aberto (fronteira da resposta genérica, SLA de revisão, RTO/RPO do restore, limiar de expansão de busca por ramo, frequência de auditoria de contradições) — e todos eles também estão listados, numerados, na Seção 8.
> - Adicionei uma **Seção 12 nova** — sumário de rastreabilidade — separando os 33 requisitos em três tipos de verificação (teste funcional automatizável, auditoria de processo, teste de infraestrutura), para já deixar o terreno preparado para quando vocês fizerem o plano de testes formal.
> - A nota de abertura do documento agora registra a versão (v2.0) e explica que 23 dos 33 requisitos tiveram critério de teste novo ou reforçado, sem carregar o histórico passo a passo da revisão (esse fica só no arquivo `revisao-qa-testabilidade-rag-novatech.md`, como registro do processo).
>
> O documento está pronto para seguir como a versão de referência do projeto. Quer que eu já parta para o próximo artefato — por exemplo, transformar os 6 requisitos ainda "condicionalmente testáveis" (Seção 12) em uma pauta de decisão para levar ao cliente, ou seguir para os ADRs técnicos com o Tech Lead?

---

## Turno 7

**Usuário:**

> agora gere um a transcrição completa desse chat em um arquivo .md

**Claude:**

*(Gerou este arquivo.)*

---

## Anexo — Arquivos referenciados nesta sessão

**Arquivos fornecidos pelo usuário (Turno 1):**
- exercicio-fase-1-entendimento.md
- discovery-simulado-stakeholders-novatech.md
- jornada-atendente-diagrama-novatech.html
- jornada-atendente-proposta-novatech.md
- anexo-a-documentacao-simulada-novatech.md
- FAQ-atendimento.md
- POL-001-politica-devolucao.md
- PROC-042-frete-especial-v1.md
- PROC-042-v2-frete-especial-revisado.md
- SLA-2024-tabela-sla-clientes.md

**Arquivos gerados por Claude ao longo da conversa:**
- `spec-requisitos-rag-novatech.md` — especificação de requisitos de produto para o pipeline de RAG (versão final v2.0, 33 requisitos, todos com critério de teste).
- `revisao-qa-testabilidade-rag-novatech.md` — revisão de QA sobre a testabilidade dos requisitos, com veredito por requisito e recomendação priorizada.
- `transcricao-chat-novatech.md` — este arquivo, transcrição completa da conversa.

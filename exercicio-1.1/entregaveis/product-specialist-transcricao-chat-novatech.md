# Transcrição do Chat — Projeto NovaTech (Assistente de IA)

**Data:** 2026-08-09
**Participantes:** Rodrigo (Product Specialist, DB1) e Claude (assistente)

---

## Turno 1

### Usuário

Como Product Especialist da DB1 que é uma empresa de soluções digitais, estou analisando um projeto para um cliente a NovaTech com o seguinte contexto:

Cenário do projeto:

"A NovaTech é uma empresa de médio porte do setor de logística com 1.200 funcionários. Sua operação depende de um conjunto extenso de documentação interna: manuais de procedimento operacional, políticas de compliance, tabelas de SLA por tipo de cliente, regras de cálculo de frete, e normas de segurança de carga.
Hoje, essa documentação está espalhada em três fontes: um SharePoint corporativo com ~800 documentos (PDFs e Word), uma wiki interna no Confluence com ~400 páginas, e uma pasta de rede com planilhas de referência atualizadas mensalmente.
O problema: a equipe de atendimento ao cliente (45 pessoas) gasta em média 12 minutos por chamado buscando informações nessas fontes para responder dúvidas de clientes sobre prazos, regras de frete, políticas de devolução e procedimentos de reclamação. Isso gera atrasos, respostas inconsistentes e frustração tanto dos atendentes quanto dos clientes.
A NovaTech contratou a DB1 para construir um assistente de IA que permita aos atendentes fazer perguntas em linguagem natural e receber respostas fundamentadas na documentação oficial da empresa, com indicação da fonte. O assistente será integrado ao ambiente Microsoft da NovaTech (Teams + SharePoint)."

Armazene o cenário do projeto, para que façamos uma análise de intenção do projeto a partir de mais informações que serão disponibilizadas pelo cliente

### Assistente

Cenário armazenado. Registrei os pontos-chave — as três fontes de documentação (SharePoint, Confluence, pasta de rede), o problema quantificado (45 atendentes, 12 min/chamado), e o escopo contratado (assistente de IA com RAG sobre a documentação, integrado a Teams + SharePoint, com citação de fonte).

Fico no aguardo das informações adicionais do cliente para seguirmos com a análise de intenção do projeto.

*(Arquivo gerado: `projeto-novatech-cenario.md`)*

---

## Turno 2

### Usuário

Agora vamos dividir nosso trabalho de análise de artefatos em três etapas, cada uma o cliente irá fornecer alguns artefatos que iremos trabalhar.

Etapa 1: Visão geral - nessa etapa o cliente disponibilizou os metadados de alguns documentos, a partir daí faça um mapa de temas cobertos e hipóteses de gaps:

1. "POL-001: Política de Devolução de Mercadorias" — Define regras para devolução em até 7 dias, com exceções para carga perigosa.
2. "PROC-042: Procedimento de Cálculo de Frete Especial" — Fórmula para fretes acima de 500kg com tabela de multiplicadores por região.
3. "SLA-2024: Tabela de SLA por Tipo de Cliente" — Prazos de resposta diferenciados para clientes Gold, Silver e Standard.
4. "PROC-042-v2: Procedimento de Cálculo de Frete (Revisado)" — Mesma numeração do item 2, mas com multiplicadores diferentes. Sem indicação de qual é o vigente.
5. "FAQ-Atendimento: Perguntas Frequentes do Time de Suporte" — Documento informal com 47 perguntas e respostas escritas por atendentes experientes, sem validação formal.

### Assistente

O ponto mais crítico que saltou aos olhos: **PROC-042 e PROC-042-v2 têm a mesma numeração, multiplicadores diferentes e nenhuma indicação de qual está vigente** — se isso não for resolvido antes da indexação, o assistente reproduz exatamente o problema que o projeto quer resolver (respostas inconsistentes). Também sinalizei a ausência de um documento formal de **procedimento de reclamação** (um dos quatro temas centrais do problema original) e o risco de tratar o **FAQ informal** com a mesma autoridade de uma política oficial.

O documento tem uma seção com perguntas sugeridas para a próxima rodada com o cliente — pode ser um bom roteiro para a Etapa 2. Fico no aguardo dos próximos artefatos.

*(Arquivo gerado e entregue: `etapa1-visao-geral-novatech.md` — mapa de temas cobertos e hipóteses de gaps)*

---

## Turno 3

### Usuário

*(Upload dos arquivos `PROC042freteespecialv1.md` e `PROC042v2freteespecialrevisado.md`)*

Conforme sua sugestão na Etapa 2, solicitei ao cliente os documentos: PROC-042 e PROC-042-v2, para que faça uma análise de inconsistências.

### Assistente

O achado central: o campo "Status" de ambos os documentos diz que não há versão vigente formalmente definida, mas a Seção 5 da v2 ("Disposições Transitórias") já opera como se ela fosse a sucessora oficial desde 01/12/2023 — uma contradição entre metadado e conteúdo. Como hoje é agosto/2026, esse período de transição já encerrou há mais de dois anos, então na prática a v1 não tem mais nenhum uso válido pelas próprias regras da v2.

Também calculei o efeito líquido: embora os fatores de peso tenham caído na v2, os multiplicadores regionais subiram mais que proporcionalmente — o frete da v2 nunca fica mais barato que o da v1 (varia de 0% a +12,5% a mais, conforme região e faixa de peso). E a v2 sinaliza que a PROC-043 (cargas perigosas) está em revisão pelo Compliance — vale pedir esse documento também antes de fechar o desenho da base de conhecimento.

*(Arquivo gerado e entregue: `etapa2-inconsistencias-proc042.md` — análise de inconsistências v1 x v2, com cálculo de impacto no valor do frete)*

---

## Turno 4

### Usuário

*(Upload dos arquivos `FAQatendimento.md`, `POL001politicadevolucao.md` e `SLA2024tabelaslaclientes.md`)*

Agora na etapa 3 já temos o FAQ-Atendimento completo (POL-001, SLA-2024 e FAQ-Atendimento), que complementa nosso contexto, analisei as Etapas 1 e 2 juntamente com esses novos artefatos procurando inconsistências e oportunidades.

### Assistente

O achado mais forte desta etapa: o item 45 do FAQ ("desconto a partir de 10 fretes/mês") não bate nem com a v1 nem com a v2 do PROC-042 — mistura o limiar da v1 (10 fretes) com a mecânica automática da v2. Isso é prova concreta de que a ambiguidade de versão já está gerando informação errada em uso real, com um cliente de 8-9 fretes/mês sendo informado de que não tem direito a desconto quando, pela v2, teria.

Também apareceu um processo de sinistro/carga danificada (janela de 48h, e-mail sinistros@novatech.com.br, envolvimento do Jurídico) que não existe em nenhuma linha do POL-001 — ou há um documento próprio que não recebemos, ou é uma prática paralela não formalizada. E o seguro de carga (0,3%/0,8%) aparece só no FAQ, sem nenhum lastro oficial na amostra.

Do lado positivo, três itens do FAQ (devolução de carga perigosa, inexistência do tier Platinum, e a distinção SLA de resposta x resolução) bateram exatamente com POL-001 e SLA-2024 — bons candidatos a virarem respostas "certificadas" do assistente desde já, sem esperar a validação completa das 47 perguntas.

O relatório traz a lista de próximos documentos a pedir ao cliente (PROC-043, PROC-088, política de seguro de carga, política de sinistros, e o restante do FAQ).

*(Arquivo gerado e entregue: `etapa3-cruzamento-pol001-sla-faq.md` — cruzamento de POL-001, SLA-2024 e FAQ-Atendimento com os achados das Etapas 1 e 2)*

---

## Turno 5

### Usuário

Como as análise dos artefatos de forma parcial encontrou inconsistências e pontos de atenção importantes, montar um texto para que eu exponha isso de forma clara ao cliente e montar também um mapa de risco caso não sejam feitas novas análises e um discovery com stakeholders envolvidos no processo principalmente em relação a governança de dados.

### Assistente

*(Turno interrompido pelo usuário durante a montagem do mapa de risco visual e do documento Word, antes de uma resposta final ser enviada. O gráfico de matriz de risco chegou a ser gerado — `mapa_risco_novatech.png` — mas o texto e o documento consolidado não foram concluídos nesta etapa.)*

---

## Turno 6

### Usuário

*(Interrupção do turno anterior)* Conforme as inconsistência encontradas na fase 1 e 2, preparar um texto para o cliente que exponha a necessidade de um discovery para melhorar a governança de dados levando em conta também o volume de informações (context rot) do projeto.

### Assistente

O texto está pronto para envio à NovaTech. Estrutura o argumento em três movimentos: o que a análise já encontrou mesmo numa amostra pequena (conflito PROC-042 com impacto real comprovado, ausência de metadados de governança), por que o volume do acervo (~1.200 documentos, 3 fontes, atualização mensal) faz esse problema piorar com a escala em vez de diluir — introduzindo o conceito de "context rot" em linguagem acessível — e a proposta concreta do discovery com as áreas dono das fontes (Comercial, Operações, Compliance, Jurídico, TI, Atendimento).

Deixei de fora o mapa de risco visual que estava montando antes da sua interrupção (o gráfico já está pronto, caso queira retomar aquela frente depois). Se quiser, posso anexá-lo como apoio visual a este mesmo texto, ou seguir para a próxima etapa de artefatos.

*(Arquivo gerado e entregue: `texto-cliente-discovery-governanca.md` — texto pronto para uso com a NovaTech)*

---

## Turno 7

### Usuário

preciso que gere um arquivo .md com todas as minhas entradas e as respostas deste chat

### Assistente

*(Este documento — `transcricao-chat-novatech.md` — gerado e entregue em resposta a este pedido.)*

---

## Arquivos gerados ao longo do chat

- `projeto-novatech-cenario.md` — cenário do projeto
- `etapa1-visao-geral-novatech.md` — mapa de temas e hipóteses de gaps
- `etapa2-inconsistencias-proc042.md` — análise de inconsistências PROC-042 v1 x v2
- `etapa3-cruzamento-pol001-sla-faq.md` — cruzamento POL-001, SLA-2024 e FAQ-Atendimento
- `texto-cliente-discovery-governanca.md` — texto para o cliente sobre discovery de governança e context rot
- `mapa_risco_novatech.png` — matriz de risco (gerada, ainda não incorporada a um documento final)
- `transcricao-chat-novatech.md` — este arquivo

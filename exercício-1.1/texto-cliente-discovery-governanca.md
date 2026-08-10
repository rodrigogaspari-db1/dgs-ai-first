## Assunto: Por que precisamos de um discovery de governança de dados antes de escalar a base do assistente

Prezados,

Nas últimas semanas conduzimos, junto ao time da DB1, uma análise preliminar sobre uma pequena amostra da documentação que hoje sustenta o atendimento da NovaTech — a política de devolução, a tabela de SLA e o procedimento de cálculo de frete especial, este último em duas versões distintas. O objetivo não era auditar cada documento em detalhe, e sim entender, antes de começarmos a construir a base de conhecimento do assistente, se a documentação de origem está em condição de sustentar respostas confiáveis.

O resultado dessa análise preliminar nos deixou atentos a um ponto que consideramos importante trazer à mesa agora, antes de avançarmos.

### O que encontramos, mesmo em uma amostra pequena

Analisamos meia dúzia de documentos — uma fração mínima do acervo total — e já identificamos duas versões do procedimento de cálculo de frete especial coexistindo sem qualquer indicação formal de qual está vigente, com fórmulas de cálculo diferentes entre si. Ao cruzar esse achado com o material de apoio usado hoje pelo time de atendimento, confirmamos que essa ambiguidade já afeta a operação real: a regra de desconto por volume que os atendentes aplicam atualmente não corresponde integralmente a nenhuma das duas versões oficiais do procedimento. Também não encontramos, nos documentos avaliados, nenhum campo padronizado que indique quem é o responsável por cada norma, qual é a data de vigência ou se um documento substitui outro — o que hoje é resolvido, na prática, por conhecimento informal e tácito da equipe, e não por um processo de governança.

Isso não é incomum em empresas do porte da NovaTech, e não é um problema que a DB1 espera que já estivesse resolvido — é exatamente o tipo de situação que uma análise cuidadosa antes da construção do assistente deveria revelar. O ponto de atenção é outro: esse padrão, encontrado em uma amostra mínima, tende a se repetir — e a se agravar — na medida em que olharmos para o volume total de documentação envolvido no projeto.

### O efeito da escala: por que mais dados não significam mais qualidade

O acervo documental da NovaTech soma cerca de 800 documentos no SharePoint, 400 páginas no Confluence e um conjunto de planilhas de referência atualizadas todo mês. Esse volume é, ao mesmo tempo, o principal motivo pelo qual o assistente de IA é necessário — nenhum atendente consegue navegar manualmente por tudo isso em poucos minutos — e o principal risco técnico do projeto, se a base não for curada antes de ser colocada à disposição da ferramenta.

Sistemas de IA baseados em busca sobre documentação (a abordagem que estamos usando para a NovaTech) não funcionam melhor simplesmente por terem mais conteúdo disponível. Quando a base cresce sem controle de qualidade — com versões conflitantes, documentos obsoletos ainda ativos, conteúdo informal misturado a normas oficiais e informação duplicada em fontes diferentes —, a precisão do assistente tende a cair, não a subir. Cada documento conflitante ou desatualizado que entra na base é mais uma chance de o assistente recuperar a informação errada e apresentá-la com a mesma confiança de uma resposta correta. É um efeito bem conhecido em projetos de IA sobre grandes volumes de documentação, chamado de "context rot" (degradação por excesso de contexto): a qualidade das respostas piora à medida que a quantidade de informação mal curada aumenta, mesmo que a tecnologia por trás do assistente seja excelente.

No caso da NovaTech, isso é particularmente relevante porque já comprovamos, em uma fração pequena do acervo, exatamente os três ingredientes que causam esse efeito: versões conflitantes sem hierarquia definida, processos reais não documentados formalmente, e conhecimento informal sem validação circulando com a mesma autoridade de uma política oficial. Projetar esse padrão para os cerca de 1.200 documentos do acervo — e para as atualizações mensais das planilhas, que continuam adicionando novas versões ao longo do tempo — é o que nos leva a recomendar um passo adicional antes de seguirmos para a construção em escala da base de conhecimento.

### O que propomos: um discovery de governança de dados

Recomendamos um discovery estruturado, com a participação dos principais responsáveis pelas fontes de informação — áreas como Comercial, Operações, Compliance, Jurídico e o time responsável pelo SharePoint e Confluence, além do próprio time de atendimento. O objetivo desse discovery não é revisar documento por documento (isso seguimos fazendo em paralelo, como temos feito nestas primeiras etapas), mas responder a perguntas que hoje não têm dono claro: quem decide qual versão de um documento é a vigente quando há conflito; como e quando um documento deve ser formalmente substituído ou arquivado; qual o papel do conhecimento informal e tácito do time de atendimento — deve virar documentação oficial, deve ser tratado como fonte secundária, ou não deve alimentar o assistente; e quem mantém essa governança funcionando à medida que novos documentos e novas versões continuam sendo produzidos.

Sem essas respostas, cada novo lote de documentos que analisarmos tende a revelar o mesmo tipo de inconsistência que já encontramos nestas primeiras etapas — e o esforço de curadoria manual, feito documento a documento, não escala para os 1.200 documentos do acervo. Com essas respostas definidas, conseguimos desenhar a base de conhecimento do assistente já prevendo como resolver conflitos automaticamente, o que reduz o risco de o assistente herdar — e amplificar, para as 45 pessoas do time de atendimento simultaneamente — os mesmos problemas que hoje o projeto busca eliminar.

Sugerimos agendar uma sessão de discovery com esses stakeholders ainda nesta fase do projeto, antes de avançarmos para a ingestão do restante da documentação. Ficamos à disposição para propor uma pauta e conduzir essa conversa junto às áreas envolvidas.

Atenciosamente,
Equipe DB1 — Projeto Assistente de IA NovaTech

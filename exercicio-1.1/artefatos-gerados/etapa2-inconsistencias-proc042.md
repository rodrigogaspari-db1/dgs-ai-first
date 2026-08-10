# Análise de Artefatos — NovaTech
## Etapa 2: Análise de Inconsistências — PROC-042 (v1) x PROC-042-v2

**Data:** 2026-08-09
**Insumo:** Conteúdo completo dos documentos PROC-042 v1 (emitido 03/03/2023) e PROC-042-v2 (emitido 10/11/2023)

---

## 1. Divergências campo a campo

| Item | v1 | v2 | Divergência |
|---|---|---|---|
| Fator de peso — 1.001 a 3.000kg | 1.2 | 1.15 | Reduzido |
| Fator de peso — acima de 3.000kg | 1.5 | 1.4 | Reduzido |
| Multiplicador regional — todas as regiões | Base original | +0,1 (Sul, Sudeste, Centro-Oeste, Nordeste) / +0,2 (Norte) | Aumentado em todas as regiões |
| Prazo de entrega (adicional) | +2 dias úteis | +3 dias úteis | Aumentado — **mudança reconhecida explicitamente no próprio texto da v2** |
| Desconto por volume | Negociação ad hoc a partir de 10 fretes/mês, via aditivo contratual | Tabela fixa: 5% a partir de 8 fretes/mês; 10% acima de 15 fretes/mês; descontos maiores exigem aprovação da Diretoria | Mudança de mecanismo (de negociação para regra fixa) e de limiar (10 → 8) |
| Cargas perigosas | Remete à PROC-043, sem ressalvas | Remete à PROC-043, **com nota de que está em revisão pelo Compliance** | Informação nova — risco de uma terceira inconsistência futura |
| Disposições transitórias | Não existe | Seção 5 nova: define regra de corte em 01/12/2023 (chamados antigos usam v1, novos usam v2) | Campo inexistente na v1 |

---

## 2. Impacto líquido no valor do frete

Os fatores de peso e os multiplicadores regionais mudam em direções opostas (peso cai, regional sobe), então o efeito final não é óbvio à primeira vista. Calculei as 15 combinações de região × faixa de peso (fórmula: multiplicador regional × fator de peso) para comparar v1 e v2:

| Faixa de peso | Menor variação (região) | Maior variação (região) |
|---|---|---|
| 500–1.000kg | +7,14% (Nordeste) | +12,5% (Norte) |
| 1.001–3.000kg | +2,68% (Nordeste) | +7,81% (Norte) |
| Acima de 3.000kg | 0% (Nordeste — empate exato) | +5,0% (Norte) |

**Conclusão verificada por cálculo:** em nenhuma das 15 combinações o frete da v2 fica mais barato que o da v1 — varia de 0% (Nordeste, acima de 3.000kg, empate exato) a +12,5% (Norte, 500–1.000kg), com média de +5,19%. Ou seja, apesar de os fatores de peso terem caído, o aumento dos multiplicadores regionais domina o resultado: **a v2 é sistematicamente igual ou mais cara que a v1**, nunca mais barata. Isso é relevante porque, se o assistente responder um cliente usando a fórmula errada, o erro tende a ser "subcobrar" (usar v1 quando deveria usar v2), gerando prejuízo à NovaTech, não ao cliente.

---

## 3. Contradição central: governança x conteúdo operacional

Este é o achado mais importante desta etapa.

- O campo **Status** de ambos os documentos afirma explicitamente que **não há indicação formal de qual versão é vigente** e que "ambos coexistem no SharePoint sem hierarquia clara".
- Porém, a **Seção 5 (Disposições Transitórias)**, presente apenas na v2, já opera na prática como se a v2 fosse a versão corrente: define uma data de corte (01/12/2023) a partir da qual todo chamado novo deveria usar os multiplicadores da v2, mantendo a v1 apenas para chamados abertos antes dessa data e ainda em processamento.

Ou seja: **o conteúdo do próprio documento contradiz o metadado de status.** Operacionalmente, a área de negócio (Diretoria Comercial, responsável por ambas as versões) já tratou a v2 como sucessora da v1 desde novembro/2023 — só não formalizou isso no campo de governança do documento.

Reforça essa leitura: a v2 é posterior (emitida em 10/11/2023, contra 03/03/2023 da v1), referencia explicitamente valores "da versão anterior" no texto, e assume que já existe uma "versão anterior" a ser substituída.

**Implicação para a data de hoje (09/08/2026):** o período de transição definido pela própria v2 (corte em 01/12/2023) encerrou-se há mais de dois anos e meio. Qualquer chamado que estivesse "em processamento" naquela época certamente já foi encerrado. Do ponto de vista prático, **não existe mais nenhum cenário válido, pelas regras da própria v2, em que a v1 deveria ser usada hoje.**

---

## 4. Recomendações

1. **Para a base de conhecimento do assistente:** indexar apenas a PROC-042-v2 como fonte ativa para cálculo de frete especial. A v1 não deve ser recuperável nas respostas padrão do assistente — se for mantida no repositório por motivo de auditoria/histórico, deve ficar em uma coleção separada, com tag temporal explícita ("histórico — válido apenas para chamados abertos antes de 01/12/2023"), fora do fluxo de busca padrão.
2. **Para a NovaTech (ação de governança, fora do escopo técnico da DB1, mas a ser formalmente recomendada):** atualizar o campo Status da v2 para declarar explicitamente "substitui PROC-042 v1, vigente desde 01/12/2023", e marcar a v1 como obsoleta no SharePoint. Isso evita que o mesmo problema se repita para outros pares de documentos do acervo de ~1.200 arquivos.
3. **Novo risco identificado, para acompanhar em etapa futura:** a v2 sinaliza que a PROC-043 (Frete de Cargas Perigosas) está em revisão pelo Compliance. Recomendo solicitar esse documento ao cliente antes de fechar o desenho da base de conhecimento, para não repetir este mesmo tipo de inconsistência com uma terceira norma.
4. **Padrão a generalizar:** este caso confirma a hipótese levantada na Etapa 1 — metadados sozinhos não são suficientes para resolver conflitos de vigência; foi necessário ler o conteúdo completo (em especial disposições transitórias) para inferir a versão correta. Vale perguntar ao cliente se outros documentos do acervo têm estrutura semelhante (cláusulas transitórias, notas de "versão anterior") que ajudem a automatizar essa checagem em escala.

---

*Próxima etapa: aguardando novos artefatos do cliente.*

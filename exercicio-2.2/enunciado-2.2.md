#### Exercício 2.2 — Governança de specs no modelo SDD

**Contexto:** O time vai usar Spec Driven Development. Specs não são documentos passivos — são contratos executáveis. Você precisa definir como specs são criadas, aprovadas, versionadas e rastreadas.

**Ferramentas a utilizar:** Claude (chat) + Claude Cowork

**Inputs fornecidos:**
- O cenário completo.
- A estrutura do repositório do projeto (ver **Anexo C**) — as specs devem seguir a organização de diretórios definida.
- O fluxo SDD simplificado: *"requirements.md define o que precisa ser feito. plan.md define como será feito. tasks.md decompõe em unidades atômicas executáveis por agentes. Cada transição (requirements → plan → tasks) é um checkpoint humano."*
- Uma lista dos módulos do projeto que precisarão de specs:
  1. Pipeline de ingestão de documentos
  2. API de busca (query endpoint)
  3. API de feedback (atendente reporta resposta incorreta)
  4. Bot do Teams (interface conversacional)
  5. Painel web (dashboard de métricas e histórico)

**Tarefa:**
1. Usando o **Claude**, defina um processo de governança de specs que cubra: quem cria cada tipo de spec (requirements pelo Product Specialist, plan pelo Tech Lead, tasks pelo Dev com apoio do Copilot), como as specs são nomeadas e versionadas, onde ficam no repositório, e como mudanças são rastreadas.

2. Usando o **Claude Cowork**, crie um board de tracking (template de kanban ou tabela) que permita acompanhar o status de cada spec: Rascunho → Em Revisão → Aprovada → Em Implementação → Validada. Inclua os 5 módulos como itens iniciais.

3. Defina o que acontece quando uma spec precisa mudar depois de já estar em implementação (change management).

**Entregável:** O documento de governança, o board de tracking gerado pelo Cowork, e o processo de change management.

**Critérios de avaliação:**
- O processo reconhece que specs são artefatos vivos que evoluem (não são documentos estáticos escritos uma vez).
- O board é prático e permite que qualquer membro do time veja o status atual de cada spec.
- O processo de change management é explícito sobre quem pode alterar, quem precisa aprovar, e como isso afeta tasks já em andamento.
- A atribuição de responsabilidades por papel é coerente com as competências de cada um.

---

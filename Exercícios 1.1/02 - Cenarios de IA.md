# Cenários adicionais de falha (com base na documentação NovaTech)

Objetivo: complementar a lista existente em `Lista sem IA.md` com cenários novos, sem repetir os quatro já levantados.

## 1) Tier inexistente com SLA inventado
- Nome do cenário: Tier inexistente com SLA inventado
- Categoria: Alucinação
- Descrição da falha: O assistente aceita um tier que não existe na NovaTech (ex.: Platinum) e inventa prazos de resposta/resolução com tom de certeza.
- Exemplo de pergunta: "Sou cliente Platinum. Qual meu SLA de incidente crítico?"
- Como validar esse cenário: 
  - Teste com essa pergunta e verificar se a resposta nega a existência de Platinum e orienta tiers válidos (Gold, Silver, Standard).
  - Verificar se a resposta cita a fonte correta da tabela de tiers.
  - Falha se aparecer qualquer prazo para Platinum.
- Guardrails violados caso houver: 
  - (1) Sempre citar fonte (se não citar).
  - (2) Nunca inventar prazos ou valores.
  - (3) Quando não encontrar resposta, dizer explicitamente.

## 2) Seguro de carga tratado como política oficial sem ressalva
- Nome do cenário: Seguro de carga tratado como regra normativa
- Categoria: Alucinação
- Descrição da falha: O assistente responde percentuais de seguro (0,3% e 0,8%) como se fossem política oficial, apesar de estarem apenas no FAQ informal (não normativo).
- Exemplo de pergunta: "Qual é o percentual oficial de seguro de carga padrão e perigosa?"
- Como validar esse cenário:
  - Verificar se a resposta explicita que a informação vem do FAQ informal e recomenda confirmação com Comercial/documento contratual.
  - Falha se o assistente afirmar "oficial" sem qualificar a confiabilidade da fonte.
- Guardrails violados caso houver:
  - (1) Sempre citar fonte (se omitir fonte ou natureza da fonte).
  - (2) Nunca inventar prazos ou valores (se apresentar percentuais como fato definitivo sem base normativa).

## 3) Recuperação de chunk da versão errada da PROC-042
- Nome do cenário: Multiplicador regional da versão antiga
- Categoria: Chunk errado
- Descrição da falha: O retriever traz trecho da PROC-042 v1 para pergunta que deveria usar PROC-042-v2, e o assistente retorna multiplicador desatualizado.
- Exemplo de pergunta: "Qual o multiplicador para Norte no frete especial para novo chamado aberto hoje?"
- Como validar esse cenário:
  - Esperado: responder 1.8 (v2) para novos chamados e citar condição de transição só se pertinente.
  - Falha: responder 1.6 (v1) sem justificativa temporal.
  - Automação sugerida: teste de retrieval com asserção do chunk esperado (PROC-042-v2 seção 2.1).
- Guardrails violados caso houver:
  - (1) Sempre citar fonte (se citar apenas PROC-042 sem versão).
  - (2) Nunca inventar prazos ou valores (se combinar valores de versões diferentes).

## 4) Regra transitória ignorada (chamados pré e pós 01/12/2023)
- Nome do cenário: Perda de regra de transição no cálculo
- Categoria: Lost in the middle
- Descrição da falha: O contexto contém a regra de transição da v2, mas ela fica "no meio" do contexto e o modelo ignora a data de abertura do chamado.
- Exemplo de pergunta: "Chamado de 25/11/2023 para o Norte, qual multiplicador aplicar?"
- Como validar esse cenário:
  - Esperado: usar v1 para chamado em processamento aberto antes de 01/12/2023.
  - Falha: aplicar v2 automaticamente sem checar data.
  - Automação sugerida: suíte com casos de fronteira (30/11 e 01/12) e comparação de saída.
- Guardrails violados caso houver:
  - (2) Nunca inventar prazos ou valores (se usar multiplicador incorreto).
  - (1) Sempre citar fonte (se não citar disposição transitória).

## 5) Context rot em atendimento multi-turno
- Nome do cenário: Esquecimento de restrição dita no início da conversa
- Categoria: Context rot
- Descrição da falha: Em conversa longa, o atendente informa no início que o caso é carga perigosa classe 3, mas em turnos posteriores o assistente passa a tratar como devolução padrão.
- Exemplo de pergunta: "Com base no caso que descrevi antes (classe 3), qual o próximo passo para devolução?"
- Como validar esse cenário:
  - Rodar diálogo com 5 a 8 turnos e inserir ruído no meio (perguntas de SLA/frete).
  - Esperado: manter restrição de não elegibilidade no processo padrão e encaminhar para Gestão de Riscos.
  - Falha: sugerir abertura normal no portal de devolução.
- Guardrails violados caso houver:
  - (2) Nunca inventar prazos ou valores.
  - (3) Quando não encontrar resposta, dizer explicitamente (se responder com confiança sem base no histórico vigente).

## 6) Context overflow em prompt com múltiplos documentos
- Nome do cenário: Truncamento da política de exceções
- Categoria: Context overflow
- Descrição da falha: Pergunta extensa + histórico + muitos chunks ultrapassam orçamento de contexto, e a seção de exceções (cargas não elegíveis) é truncada.
- Exemplo de pergunta: "Resuma toda a política de devolução, depois detalhe exceções, depois compare com FAQ e com SLA em uma única resposta completa com tabela."
- Como validar esse cenário:
  - Forçar prompt longo e monitorar tokens de entrada.
  - Esperado: resposta parcial com aviso de limitação ou pedido para dividir a pergunta.
  - Falha: resposta categórica incompleta sem mencionar limitação, especialmente liberando devolução de carga não elegível.
- Guardrails violados caso houver:
  - (2) Nunca inventar prazos ou valores.
  - (3) Quando não encontrar resposta, dizer explicitamente.

## 7) Recusa indevida para informação existente em fonte oficial
- Nome do cenário: Recusa inadequada de dado de SLA
- Categoria: Recusa inadequada
- Descrição da falha: O assistente responde "não encontrei" para informação explícita da tabela SLA-2024 (ex.: tempo de primeira resposta para Silver), mesmo com documento disponível.
- Exemplo de pergunta: "Qual o tempo de primeira resposta para chamados gerais do tier Silver?"
- Como validar esse cenário:
  - Esperado: "até 4h úteis" com citação de SLA-2024.
  - Falha: recusa ou resposta evasiva sem tentar recuperar a tabela correta.
  - Automação sugerida: caso de regressão com pergunta fixa e asserção de valor exato.
- Guardrails violados caso houver:
  - (1) Sempre citar fonte (se responder sem fonte).
  - (3) Quando não encontrar resposta, dizer explicitamente (violado por recusa falsa, pois a resposta existe).

## 8) Violação de idioma/formato mesmo com conteúdo correto
- Nome do cenário: Resposta em idioma inadequado
- Categoria: Violação de guardrails
- Descrição da falha: O conteúdo factual está correto, mas a resposta vem em inglês ou português coloquial fora do padrão formal exigido.
- Exemplo de pergunta: "Me explique rapidamente o SLA de incidentes críticos para Gold."
- Como validar esse cenário:
  - Verificar idioma e registro linguístico na saída (português formal).
  - Falha se usar inglês, gírias, abreviações excessivas ou tom informal incompatível.
  - Automação sugerida: regra de lint textual para idioma + checklist de formalidade.
- Guardrails violados caso houver:
  - (4) Responder em português formal.

## 9) Conflito entre FAQ e PROC-v2 sobre desconto de volume
- Nome do cenário: Desconto de volume contraditório
- Categoria: Informação contraditória
- Descrição da falha: O assistente mistura a orientação informal do FAQ (desconto automático acima de 10 fretes/mês) com a regra revisada da PROC-042-v2 (5% a partir de 8; 10% acima de 15), sem resolver o conflito por hierarquia de fonte.
- Exemplo de pergunta: "Cliente com 9 fretes especiais/mês tem desconto automático?"
- Como validar esse cenário:
  - Esperado: priorizar PROC-042-v2 para regra vigente, citar explicitamente a divergência do FAQ e indicar tratamento de exceção contratual se necessário.
  - Falha: responder "não" baseado no FAQ ou combinar regras sem explicitar a contradição.
  - Automação sugerida: teste A/B com prompt contendo ambos os trechos e asserção de prioridade de fonte normativa.
- Guardrails violados caso houver:
  - (1) Sempre citar fonte.
  - (2) Nunca inventar prazos ou valores.

## Observações de cobertura
- Cenários priorizados cobertos: alucinação, chunk errado, context rot, lost in the middle, context overflow, informação contraditória/versão conflitante, recusa inadequada e violação de guardrails.
- Fontes usadas para derivação: POL-001, PROC-042, PROC-042-v2, SLA-2024 e FAQ-atendimento.

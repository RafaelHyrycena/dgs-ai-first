# Rubrica de Avaliação de Respostas do Assistente RAG (NovaTech)

## Objetivo
Avaliar, de forma consistente e reutilizável, a qualidade de qualquer resposta do assistente RAG da NovaTech.

## Escala Geral
- Escala por dimensão: **1 a 3**
- Interpretação: **1 = inadequado**, **2 = parcialmente adequado**, **3 = adequado**

## Rubrica Analítica (4 dimensões)

| Dimensão | Nota 1 (Inadequado) | Nota 2 (Parcialmente adequado) | Nota 3 (Adequado) |
|---|---|---|---|
| **Precisão Factual** | Contém erro factual relevante, contradição com documento oficial, informação desatualizada tratada como atual, ou alucinação (ex.: criação de tier de SLA inexistente). | Conteúdo majoritariamente correto, mas com imprecisão pontual que não altera totalmente a conclusão, ou falta de atualização explícita quando há conflito entre versões. | Conteúdo totalmente consistente com a fonte oficial vigente, sem alucinações, sem contradições e sem uso de versão obsoleta como verdade atual. |
| **Citação de Fonte** | Não cita fonte, cita documento inexistente, ou cita fonte incompatível com a resposta. Inclui caso de seção claramente errada que não sustenta a afirmação. | Cita documento correto, mas com referência incompleta, ambígua, ou com seção imprecisa; a rastreabilidade fica parcial. | Cita documento correto e referência localizável (ex.: seção/tópico), alinhada ao conteúdo afirmado, permitindo verificação objetiva por outro analista. |
| **Aderência aos Guardrails** | Viola um ou mais guardrails críticos: inventa prazo/valor/política; não informa ausência de informação quando aplicável; não responde em português formal; não cita fonte. | Atende parcialmente aos guardrails: sem invenção explícita, mas com linguagem pouco formal, ou tratamento incompleto de lacuna de informação, ou citação presente porém fraca. | Cumpre integralmente os guardrails: cita fonte, não inventa dados, informa explicitamente quando não há base documental suficiente e mantém português formal. |
| **Completude** | Resposta incompleta para a pergunta: omite condição central, exceção relevante (ex.: restrição de devolução), ou parte essencial para decisão operacional. | Responde ao núcleo da pergunta, mas omite detalhe importante (condição, limite, exceção, contexto de aplicação). | Responde integralmente à pergunta com escopo correto, incluindo condições, exceções e limites necessários para uso seguro no atendimento. |

## Tabela Resumo (uso rápido)

| Dimensão | O que verifica | Evidência mínima esperada para nota 3 |
|---|---|---|
| **Precisão Factual** | Se o conteúdo está correto e atualizado conforme base oficial | Nenhuma contradição factual; nenhuma alucinação; alinhamento com versão vigente |
| **Citação de Fonte** | Se a resposta é rastreável até a origem documental correta | Documento e seção/tópico compatíveis com a afirmação |
| **Aderência aos Guardrails** | Se cumpre regras obrigatórias de segurança e formato | 4 guardrails atendidos simultaneamente |
| **Completude** | Se a resposta cobre tudo que a pergunta exige | Resposta inclui regra principal + exceções/limites aplicáveis |

## Pontuação
- **Pontuação máxima possível:** **12 pontos** (4 dimensões x nota máxima 3)
- **Pontuação mínima para aprovação:** **10 pontos**, com as seguintes condições adicionais:
  - Nenhuma dimensão com nota 1 em **Precisão Factual**.
  - Nenhuma dimensão com nota 1 em **Citação de Fonte**.
  - Ausência de falha bloqueante.

## Falhas Bloqueantes
Se ocorrer qualquer item abaixo, a resposta deve ser **reprovada**, independentemente da pontuação total:

1. **Alucinação crítica**: invenção de prazo, valor, política, tier de SLA ou regra operacional não existentes.
2. **Contradição direta da fonte oficial**: afirmação oposta ao que está no documento vigente.
3. **Ausência de fonte** quando a resposta traz afirmação factual verificável.
4. **Fonte inválida ou incompatível**: documento/seção citados não sustentam o conteúdo afirmado.
5. **Omissão de exceção de segurança/compliance** que altera a decisão (ex.: exceção explícita de devolução).
6. **Não reconhecer lacuna documental**: quando não há base suficiente e o assistente responde como se houvesse.

## Procedimento de Aplicação (padronização entre analistas)

1. Leia a pergunta e identifique o escopo exato do que foi solicitado.
2. Compare a resposta com a documentação oficial vigente.
3. Valide a citação: documento + seção/tópico sustentam cada afirmação principal.
4. Atribua nota 1-3 em cada dimensão usando estritamente os critérios da rubrica.
5. Verifique falhas bloqueantes antes de fechar o resultado.
6. Registre justificativa curta por dimensão (1 a 2 frases) para auditoria.
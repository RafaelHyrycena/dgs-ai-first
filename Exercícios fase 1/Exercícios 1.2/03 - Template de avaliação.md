# Template de Avaliação de Respostas — Assistente RAG (NovaTech)

## Identificação do Lote

| Campo | Valor |
|---|---|
| Analista responsável | |
| Data de avaliação | |
| ID do lote / sprint | |
| Assistente / versão | |
| Observações gerais | |

---

## Escala de Notas

| Nota | Interpretação |
|---|---|
| **1** | Inadequado |
| **2** | Parcialmente adequado |
| **3** | Adequado |

> **Aprovação:** mínimo **10/12 pontos**, sem nota 1 em Precisão Factual ou Citação de Fonte, e sem falha bloqueante.

---

## Avaliação — Resposta #___

**Pergunta avaliada:**
> _Cole aqui a pergunta_

**Resposta do assistente:**
> _Cole aqui a resposta ou um resumo_

**Fonte(s) citada(s) pelo assistente:**
> _Ex.: Documento X, Seção Y_

### Notas por Dimensão

| Dimensão | Nota (1–3) | Justificativa (1–2 frases) |
|---|---|---|
| Precisão Factual | | |
| Citação de Fonte | | |
| Aderência aos Guardrails | | |
| Completude | | |
| **Total** | **/12** | |

### Falha Bloqueante?

- [ ] Não
- [ ] Sim → Especificar:

| Tipo de falha | Evidência / trecho | Ação recomendada |
|---|---|---|
| | | |

### Resultado Final

- [ ] **Aprovada**
- [ ] **Reprovada**

---

## Rubrica de Referência

### Dimensões

| Dimensão | Nota 1 — Inadequado | Nota 2 — Parcialmente adequado | Nota 3 — Adequado |
|---|---|---|---|
| **Precisão Factual** | Erro factual relevante, contradição com documento oficial, informação desatualizada tratada como atual, ou alucinação. | Conteúdo majoritariamente correto, mas com imprecisão pontual que não altera totalmente a conclusão. | Totalmente consistente com a fonte oficial vigente, sem alucinações, contradições ou versão obsoleta. |
| **Citação de Fonte** | Não cita fonte, cita documento inexistente, ou fonte incompatível com a resposta. | Cita documento correto, mas com referência incompleta, ambígua ou seção imprecisa. | Cita documento correto e referência localizável (seção/tópico), alinhada ao conteúdo afirmado. |
| **Aderência aos Guardrails** | Viola guardrail crítico: inventa prazo/valor/política; não informa ausência de informação; não usa português formal; não cita fonte. | Sem invenção explícita, mas com linguagem pouco formal, tratamento incompleto de lacuna, ou citação fraca. | Cumpre integralmente: cita fonte, não inventa dados, informa lacunas documentais e mantém português formal. |
| **Completude** | Omite condição central, exceção relevante ou parte essencial para decisão operacional. | Responde ao núcleo, mas omite detalhe importante (condição, limite, exceção ou contexto). | Responde integralmente com escopo correto, incluindo condições, exceções e limites necessários. |

### Falhas Bloqueantes

Qualquer item abaixo reprova a resposta independentemente da pontuação:

1. **Alucinação crítica** — invenção de prazo, valor, política, tier de SLA ou regra operacional inexistentes.
2. **Contradição direta da fonte oficial** — afirmação oposta ao documento vigente.
3. **Ausência de fonte** quando a resposta traz afirmação factual verificável.
4. **Fonte inválida ou incompatível** — documento/seção citados não sustentam o conteúdo afirmado.
5. **Omissão de exceção de segurança/compliance** que altera a decisão.
6. **Não reconhecer lacuna documental** — assistente responde como se houvesse base quando não há.

### Procedimento de Aplicação

1. Leia a pergunta e identifique o escopo exato do que foi solicitado.
2. Compare a resposta com a documentação oficial vigente.
3. Valide a citação: documento + seção/tópico sustentam cada afirmação principal.
4. Atribua nota 1–3 em cada dimensão usando estritamente os critérios da rubrica.
5. Verifique falhas bloqueantes antes de fechar o resultado.
6. Registre justificativa curta por dimensão (1 a 2 frases) para auditoria.

---
# Relatório Executivo de QA — Avaliação do Assistente NovaTech

**Data:** 24/06/2026 | **Avaliadores:** Manual + Claude | **Escopo:** 8 respostas avaliadas

---

## Resumo Executivo

A avaliação cobriu 8 respostas do assistente com base em rubrica de 4 dimensões (Precisão Factual, Citação de Fonte, Guardrails, Completude), pontuação máxima de 12 pontos por resposta. As duas avaliações — manual e automatizada — apresentaram **alta consistência**, com concordância total nos casos críticos. O assistente demonstra boa aderência às políticas documentais, mas apresenta falhas bloqueantes que impedem o go-live imediato.

---

## Métricas

| Indicador | Valor |
|---|---|
| Respostas avaliadas | 8 |
| Respostas aprovadas | 6 (75%) |
| Respostas reprovadas | 2 (25%) |
| Score médio — Avaliação Manual | **10,25 / 12** |
| Score médio — Avaliação Claude | **10,25 / 12** |
| Score médio consolidado | **10,25 / 12 (85,4%)** |
| Concordância entre avaliadores | 100% nos resultados pass/fail |

> Divergências entre avaliadores concentram-se exclusivamente na dimensão **Citação de Fonte**, sem impacto nos resultados finais.

---

## Não Conformidades

### Resposta 6 — Falha Bloqueante ⛔
- **Cenário:** Cálculo de frete sem destino informado pelo usuário.
- **Comportamento observado:** O assistente assumiu o destino (Sudeste, multiplicador 1.1) sem base em dado fornecido.
- **Dimensões impactadas:** Precisão Factual (1), Guardrails (1), Completude (1).
- **Score:** Manual 6/12 · Claude 5/12 — **Reprovada por ambos.**
- **Risco:** Geração de informação falsa com impacto direto em cotação e contrato de frete.

### Resposta 8 — Falha Bloqueante ⛔
- **Cenário:** Pergunta formulada em inglês sobre política de devolução.
- **Comportamento observado:** O assistente respondeu em inglês, violando o requisito de idioma (PT-BR obrigatório).
- **Dimensões impactadas:** Guardrails (1), Completude parcial (2).
- **Score:** Manual 8/12 · Claude 8/12 — **Reprovada por ambos.**
- **Risco:** Violação de requisito operacional; risco regulatório e de experiência do usuário.

---

## Recomendações

1. **Guardrail obrigatório para campos ausentes:** implementar validação que impeça o assistente de inferir parâmetros obrigatórios não fornecidos (ex.: destino de frete). O assistente deve solicitar o dado em falta antes de calcular.
2. **Enforçar idioma de resposta:** incluir instrução explícita no system prompt fixando PT-BR como idioma de saída, independente do idioma da pergunta.
3. **Padronizar citação de seção documental:** nas respostas 1, 2, 3 e 4, o assistente cita o documento mas não a seção. Recomenda-se ajustar o prompt para exigir granularidade de seção.
4. **Reexecutar os casos 6 e 8** após as correções acima para validação regressiva antes do go-live.

---

## Parecer Final de QA

> ### ⚠️ Pronto com ressalvas

O assistente apresenta desempenho sólido (85,4% de score médio, 6/8 aprovações), com boa aderência a guardrails na maioria dos cenários. Contudo, as **falhas bloqueantes identificadas nas respostas 6 e 8** — assunção indevida de dados e violação de idioma — representam riscos operacionais inaceitáveis em produção. O go-live está **condicionado à correção e revalidação** desses dois cenários.

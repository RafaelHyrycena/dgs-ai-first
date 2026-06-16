## Testing Standards

Esta seção é normativa. Todos os agentes de IA (Claude, Copilot, Cursor e equivalentes) DEVEM seguir estas regras ao gerar ou sugerir código de teste.

---

### 1. Convenção de Nomenclatura

- É OBRIGATÓRIO o uso de blocos `describe` e `it`. Chamadas planas com `test()` NÃO DEVEM ser utilizadas.
- As descrições dos testes DEVEM ser escritas em inglês, com frases completas.
- O bloco `describe` DEVE nomear o módulo, função ou unidade sob teste.
- O bloco `it` DEVE descrever o comportamento esperado no formato: `should [behavior] when [condition]`.

```
describe('queryHandler', () => {
  it('should return a relevant answer when question matches indexed documents')
  it('should return a not-found message when no chunks match the query')
  it('should include a low-confidence warning when retrieval score is below threshold')
})
```

| Tipo de cenário    | Convenção de prefixo no `it`             |
|--------------------|------------------------------------------|
| Happy path         | `should [behavior] when [condition]`     |
| Cenário negativo   | `should return error when [condition]`   |
| Edge case          | `should handle [edge condition]`         |

---

### 2. Estrutura do Teste

Todo teste DEVE seguir o padrão **Arrange / Act / Assert** com separação explícita entre cada etapa.

- DEVE testar um único comportamento por bloco `it`.
- NÃO DEVE combinar asserções não relacionadas em um único teste.
- Cada etapa DEVE ser delimitada por uma linha em branco ou comentário inline.

```typescript
it('should return source_document field in every response', async () => {
  // Arrange
  const request = buildQueryRequest({ question: 'What is the return policy for damaged goods?' });
  server.use(mockChunkRetrieval([chunkFixtures.returnPolicy]));

  // Act
  const response = await queryHandler(request);

  // Assert
  expect(response.statusCode).toBe(200);
  expect(response.body.source_document).toBe('POL-001');
});
```

---

### 3. Asserções

- As asserções DEVEM verificar o valor, campo ou comportamento específico sendo testado.
- É PROIBIDO utilizar as seguintes asserções como única validação de um teste:
  - `expect(x).toBeDefined()`
  - `expect(x).toBeTruthy()`
  - `expect(x).not.toBeNull()`
- DEVE-SE dar preferência a: `.toBe()`, `.toEqual()`, `.toContain()`, `.toMatch()`, `.toHaveLength()`, `.toStrictEqual()`.
- Ao testar respostas de erro, DEVE-SE sempre validar tanto o código de status quanto a mensagem de erro.

```typescript
// NÃO PERMITIDO
expect(result).toBeDefined();

// OBRIGATÓRIO
expect(result.statusCode).toBe(200);
expect(result.body.answer).toContain('prazo de devolução');
expect(result.body.source_document).toBe('POL-001');
```

---

### 4. Práticas Proibidas

Testes NÃO DEVEM:

- Acessar serviços externos reais (Azure OpenAI, Azure AI Search, bancos de dados, sistema de arquivos).
- Depender da ordem de execução — todo teste DEVE ser executável de forma independente.
- Depender do horário ou data atual sem utilizar mock.
- Depender de dados externos instáveis ou não determinísticos.
- Utilizar `sleep` / `setTimeout` arbitrários para aguardar operações assíncronas.

---

### 5. Padrões de Mocking

**Chamadas HTTP**

- É OBRIGATÓRIO o uso de **MSW (Mock Service Worker)** para todas as requisições HTTP externas (Azure OpenAI, Azure AI Search, etc.).
- Os handlers do MSW DEVEM ser escopados por teste ou por bloco `describe`. Sobrescritas globais NÃO DEVEM ser utilizadas.

**Spies**

- Utilize spies (`vi.spyOn`) quando for necessário verificar que uma função foi chamada com argumentos específicos, sem substituir sua implementação original.

**Stubs**

- Utilize stubs (`vi.fn()`) para substituir uma função e controlar seu valor de retorno quando a implementação original não deve ser executada.

**Factories**

- É OBRIGATÓRIO o uso de funções factory para construir objetos de dados de teste. Objetos literais inline com dados fixos NÃO DEVEM ser utilizados.
- As factories DEVEM fornecer valores padrão coerentes e aceitar sobrescritas parciais.

```typescript
// Exemplo de factory
function buildQueryRequest(overrides: Partial<QueryRequest> = {}): QueryRequest {
  return {
    question: 'Quais documentos são necessários para solicitar uma devolução?',
    sessionId: 'session-test-001',
    ...overrides,
  };
}
```

---

### 6. Fixtures

Dados de teste reutilizáveis DEVEM ser armazenados em `tests/fixtures/`.

| Tipo de fixture        | Arquivo                                        |
|------------------------|------------------------------------------------|
| Perguntas de teste     | `tests/fixtures/queries.ts`                    |
| Chunks de contexto RAG | `tests/fixtures/chunks.ts`                     |
| Respostas esperadas    | `tests/fixtures/expectedResponses.ts`          |
| Casos de borda         | `tests/fixtures/edgeCases.ts`                  |

- Os dados de fixture DEVEM pertencer ao domínio de logística. Strings genéricas como `"test"`, `"hello"` ou `"foo"` são PROIBIDAS.
- Os chunks DEVEM refletir conteúdo documental realista (trechos de políticas, tabelas de SLA, passos de procedimentos).

```typescript
// tests/fixtures/chunks.ts
export const chunkFixtures = {
  returnPolicy: {
    id: 'POL-001-chunk-3',
    content: 'Devoluções de produtos com avaria devem ser solicitadas em até 7 dias úteis.',
    source_document: 'POL-001',
    score: 0.91,
  },
};
```

---

### 7. Coverage e CI

- A cobertura de linhas DEVE ser igual ou superior a **80%** para que o PR seja aprovado para merge.
- A cobertura de branches DEVE ser igual ou superior a **70%**.
- Os testes DEVEM passar sem avisos no GitHub Actions antes que um PR possa ser aprovado.

Um PR DEVE ser reprovado automaticamente se:

- Qualquer teste falhar no CI.
- A cobertura de linhas cair abaixo de 80%.
- Uma nova função pública ou handler exportado não tiver nenhuma cobertura de teste.

---

### 8. Diretrizes para Testes Gerados por IA

Ao gerar testes, os agentes de IA DEVEM:

- Aplicar todas as regras das seções 1 a 7 acima.
- Utilizar dados de fixture do domínio, nunca strings genéricas.
- Validar valores concretos, não apenas a existência de um resultado.
- Mockar todas as dependências externas com MSW ou `vi.fn()`.
- Estruturar cada teste com separação explícita entre Arrange, Act e Assert.

Agentes de IA NÃO DEVEM:

- Gerar testes que passam de forma trivial (ex.: `expect(result).toBeDefined()`).
- Nomear testes com descrições vagas como `'works'`, `'test'` ou `'should work'`.
- Acessar serviços reais dentro dos testes.
- Omitir blocos `describe`.

**Antes — não conforme (não gerar desta forma)**

```typescript
test('query endpoint works', async () => {
  const result = await handler({ body: '{"question": "test"}' });
  expect(result).toBeDefined();
});
```

Problemas: uso de `test()` plano sem `describe`; descrição vaga; dado de entrada genérico (`"test"`); asserção trivial que não valida nenhum comportamento; ausência de mock com MSW; sem estrutura Arrange / Act / Assert.

**Depois — conforme (gerar desta forma)**

```typescript
describe('queryHandler', () => {
  it('should return answer with source_document when question matches indexed chunks', async () => {
    // Arrange
    const request = buildQueryRequest({ question: 'What is the return deadline for damaged goods?' });
    server.use(mockChunkRetrieval([chunkFixtures.returnPolicy]));

    // Act
    const response = await queryHandler(request);

    // Assert
    expect(response.statusCode).toBe(200);
    expect(response.body.answer).toContain('7 dias úteis');
    expect(response.body.source_document).toBe('POL-001');
  });
});
```


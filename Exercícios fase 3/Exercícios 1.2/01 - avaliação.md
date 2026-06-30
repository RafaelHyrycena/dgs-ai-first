## Teste 1 — Assertions vagas

```typescript
describe('query endpoint', () => {
  it('should return a response', async () => {
    const res = await request(app)
      .post('/api/query')
      .send({ question: 'prazo devolução' });

    expect(res.status).toBe(200);
    expect(res.body).toBeDefined();
  });
});
```

### Objetivo do teste

O objetivo aparente é validar que o endpoint `/api/query` responde corretamente quando recebe uma pergunta sobre prazo de devolução.

### O que realmente está sendo testado

Atualmente o teste verifica apenas:

* que a requisição retorna HTTP **200**;
* que existe algum objeto em `res.body`.

Na prática, ele apenas garante que a API respondeu, sem validar se a resposta possui qualquer utilidade.

### O que deixa de testar

O teste não verifica nenhum comportamento importante do endpoint.

Por exemplo, ele não valida:

* se o campo `answer` existe;
* se o prazo retornado realmente é **7 dias úteis**;
* se a exceção para cargas perigosas está presente;
* se existe o campo `source_document`;
* se a fonte retornada corresponde ao documento esperado (`POL-001`);
* se o endpoint retorna o formato correto da API;
* se a resposta respeita os guardrails definidos para o projeto;
* se a IA retornou uma resposta baseada na documentação ou apenas uma informação inventada.

Além disso, o teste utiliza `toBeDefined()`, que é uma assertion extremamente genérica. Qualquer objeto diferente de `undefined` fará o teste passar.

### Impacto

É um teste de baixa qualidade porque valida apenas que "algo" foi retornado, mas não confirma se esse "algo" atende ao comportamento esperado.

---

## Teste 2 — Dados irreais

```typescript
describe('query endpoint edge cases', () => {
  it('should handle empty question', async () => {
    const res = await request(app)
      .post('/api/query')
      .send({ question: '' });

    expect(res.status).toBe(400);
  });
});
```

### Objetivo do teste

Validar que o endpoint rejeita uma requisição cuja pergunta está vazia.

### O que realmente está sendo testado

O teste confirma apenas que uma pergunta vazia retorna HTTP **400**.

É um teste válido para validação de entrada, porém extremamente limitado.

---

### O que deixa de testar

O teste não verifica:

* qual mensagem de erro foi retornada;
* se existe um código de erro padronizado;
* se o formato da resposta de erro segue o contrato da API;
* se o endpoint informa corretamente qual campo está inválido.

Além disso, ele não exercita nenhuma regra do domínio NovaTech.

Não há cenários envolvendo:

* devolução;
* SLA;
* cargas perigosas;
* cálculo de frete;
* busca documental.

Ou seja, o endpoint pode estar completamente quebrado para consultas reais e este teste continuará passando.

---

### Impacto

É um teste útil, mas insuficiente quando utilizado isoladamente.

---

## Teste 3 — Mock que mascara bug

```typescript
describe('feedback endpoint', () => {
  it('should save feedback', async () => {
    const mockCreate = jest.fn().mockResolvedValue({ id: '123' });

    const res = await request(app)
      .post('/api/feedback')
      .send({
        queryId: 'q1',
        rating: 5,
        comment: 'great'
      });

    expect(res.status).toBe(200);
    expect(mockCreate).toHaveBeenCalled();
  });
});
```

### Objetivo do teste

Verificar que o endpoint salva corretamente um feedback enviado pelo usuário.

### O que realmente está sendo testado

Na prática, o teste apenas verifica:

* que o endpoint retorna HTTP 200;
* que um mock foi chamado.

Entretanto, esse mock não está ligado à implementação real.

Além disso, o projeto utiliza **Vitest**, mas o teste foi escrito utilizando **Jest**, contrariando o padrão do repositório.

---

### O que deixa de testar

O teste não valida:

* se o feedback foi realmente persistido;
* se o repositório recebeu os parâmetros corretos;
* se o comentário foi salvo corretamente;
* se o rating foi armazenado;
* se o `queryId` foi utilizado;
* tratamento de erros;
* falha no banco de dados;
* retorno da camada de persistência.

---

### Impacto

O teste mascara defeitos reais de integração e não garante que a funcionalidade principal do endpoint esteja funcionando.

---

# Conclusão Geral

Dos três testes analisados:

* **Teste 1** apresenta assertions vagas e não valida as regras de negócio.
* **Teste 2** cobre apenas validação de entrada e possui baixa cobertura funcional.
* **Teste 3** utiliza um mock desconectado da implementação real e ainda emprega Jest em um projeto baseado em Vitest.

Como consequência, a suíte de testes pode indicar que a aplicação está saudável mesmo quando funcionalidades críticas estão incorretas ou quebradas. Para aumentar a confiabilidade dos testes, é necessário utilizar assertions específicas, dados representativos do domínio NovaTech, mocks corretamente integrados e validações que confirmem o comportamento esperado da aplicação, e não apenas sua execução.

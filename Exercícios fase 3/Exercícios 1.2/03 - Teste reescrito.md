```typescript
describe('query endpoint', () => {
  it('should return the correct return policy when asked about return deadline', async () => {
    const payload = {
      question: 'Qual o prazo de devolução?'
    };

    const res = await request(app)
      .post('/api/query')
      .send(payload);

    expect(res.status).toBe(200);

    expect(res.body).toMatchObject({
      answer: expect.any(String),
      source_document: expect.any(Array),
    });

    expect(res.body.answer).toContain('7');
    expect(res.body.answer).toContain('dias');
    expect(res.body.source_document).toContain('POL-001');
  });
});
```


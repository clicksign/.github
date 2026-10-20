### Descrição

<frase de abertura: 1–2 linhas resumindo o objetivo do PR e o problema que ele resolve>

#### O que foi alterado

- **<Área 1>**: <o que mudou nessa camada>
- **<Área 2>**: <o que mudou nessa camada>

#### Por que

<motivação, dor, contexto de negócio>

#### Como

<fluxo da implementação, decisões técnicas, trade-offs>

#### Antes / Depois

<!-- Incluir APENAS quando houver mudança de payload, contrato de API, estado, enum ou rota.
     Omitir completamente nos demais casos. -->

**Antes**
```json
...
```

**Depois**
```json
...
```

---

### Testes

- **O que foi testado**: <cenários validados, incluindo casos de borda e negativos>
- **Tipos de testes no PR**: <unitário, integração, request/e2e, etc.>

---

### Evidências

<!-- Anexe aqui um print ou GIF da correção/funcionalidade funcionando.
     Para mudanças sem impacto visual, cole a saída relevante (log, resposta da API, execução dos testes). -->

---

### Issue tracker

[Link para o card no Jira.](https://clicksign.atlassian.net/browse/{JIRA-CODE})

---

### Code Review

Como fazer o Code Review: [CONTRIBUTING.md](https://github.com/clicksign/.github/blob/main/CONTRIBUTING.md)

---

### Checklist para poder mergear

- [ ] O título segue o padrão `[ISSUE] (tipo) Mensagem`
- [ ] O código do PR inclui (ou já possui) testes para o código nele
- [ ] Nomes de arquivos seguem a [convenção do repositório](../CONTRIBUTING.md#convenção-de-nomes-de-arquivos)
- [ ] Os checks de linters estão passando
- [ ] Os checks de testes estão passando
- [ ] Evidências anexadas (quando houver mudança visível)
- [ ] Descrição ou código gerado com IA (se marcado, adicionar a tag `ai-generated`)

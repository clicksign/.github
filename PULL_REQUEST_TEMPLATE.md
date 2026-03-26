### Importante
Caso precise isolar o **pr** em uma release separada, favor incluir no titulo do PR a informação [rls_isolada]
ex:  [ARCH-123][**rls_isolada**]Atualização de Excalibur
- [Dúvidas sobre isolar pr.](https://clicksign.atlassian.net/wiki/spaces/KBTech/pages/765952001/Classificar+Release+Isolada)

### Descrição

\[Descreva o problema ou card resumidamente. Depois descreva a solução técnica para esse problema, focando no *como* e no *por que* a sua solução foi feita dessa forma. A ideia é ajudar o revisor do código a entendê-lo de forma geral e poder analisar o código já com algum contexto.\]

### Issue tracker

\[Link para o card no Jira.\]

### Code Review

[Como fazer o Code Review.](https://github.com/clicksign/.github/blob/8d623c886c2da0266d108d5fa21cf01a1c82cbc0/CONTRIBUTING.md)


### Links e observações

\[Links úteis que podem contextualizar e ajudar o revisor, por exemplo para a página de uma dependência que escolheu adicionar, ou um código que se inspirou, ou documentação externa (docs de uma API, do Vue, do Rails, etc).\]

## Checklist para poder mergear
 
> **Instruções:** Marque cada item após validá-lo de fato. Itens que não se aplicam a este PR devem ser marcados com `~N/A~`
> Este checklist é um **pré-requisito** para solicitar review. PRs com checklist vazio poderão ter a revisão adiada até que o autor valide os pontos.
 [Guia do Checklist.](https://github.com/clicksign/.github/blob/feature/JR-QE-908/PULL_REQUEST_GUIDE.md)
### Recursos AWS (SQS, BD, Dynamo, etc.)
- [ ] **Provisionamento:** As filas, tabelas ou recursos necessários já existem ou foram criados?
- [ ] **Acessos:** As permissões necessárias já foram habilitadas para o recurso selecionado?
- [ ] **Validação DevOps:** O time de infra/devops revisou a topologia de recursos para a infra?
 
### Configuração e Segurança
- [ ] **Env Vars:** As variáveis de ambiente foram cadastradas em todos os ambientes? (`Staging`, `Sandbox`, `Produção`)
- [ ] **Secrets:** Valores sensíveis (API Keys, senhas) estão no Secrets Manager e **não** no código ou env vars planas?
- [ ] **IAM:** As permissões de acesso aos novos recursos foram revisadas e seguem o princípio de menor privilégio?
 
### Banco de Dados e Estado Compartilhado
- [ ] **Migrações reversíveis:** Se há migrações de banco, elas são compatíveis com rollback? (sem remover colunas que o código anterior ainda lê, sem renomear destrutivamente)
- [ ] **Compatibilidade de formato:** Se esta mudança altera formatos de serialização (cache, sessões, filas de jobs), o formato antigo continua sendo lido/escrito durante a transição?
 
### Deploy e Resiliência
- [ ] **Disponibilidade:** O autor do PR ou representante estará disponível para o deploy assistido em Produção?
- [ ] **Plano de Rollback:** Existe um plano claro para reverter a alteração? (descreva brevemente abaixo, se aplicável)
- [ ] **Integrações:** Todas as dependências externas/internas foram validadas nos ambientes de teste?
 
### Qualidade e Testes
- [ ] **Testes unitários/integração:** O código do PR inclui (ou já possui) testes que cobrem as alterações?
- [ ] **Regressão:** Se a alteração impacta módulos ou serviços compartilhados, os testes de regressão foram executados nesses contextos?
- [ ] **Cenários de QA:** Os cenários de teste foram definidos, documentados e executados com sucesso no ambiente de staging?
- [ ] **Depência de versão:** Caso o PR dependa de uma versão especifica de outro serviço deve-se informar no canal de release-info e programar junto ao responsável pela release um deploy conjunto.
---
 
### Plano de Rollback (se aplicável)
 
<!-- Descreva brevemente como reverter esta alteração em caso de falha. Exemplo: "Reverter o deploy para a tag v2.3.1 e executar rake db:rollback STEP=1" -->
 
---
 
### Observações adicionais
 
<!-- Qualquer contexto extra que ajude o revisor: screenshots, links para tasks, decisões de arquitetura, etc. -->

### Screenshots (para mudanças de UI, se houver)

\[Adicione alguns screenshots das mudanças de UI inclusas no seu PR, se houver. Isso ajuda o revisor a entender como o usuário utilizaria o código do PR.\]

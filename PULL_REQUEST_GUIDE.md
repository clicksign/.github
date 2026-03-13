# Guia de Qualidade e Segurança para Pull Requests
 
> *"Uma corrente é tão forte quanto o seu elo mais fraco."*
 
---
 
## Por que este guia existe
 
Tivemos incidentes recentes em produção que poderiam ter sido evitados com validações simples antes do merge. Incompatibilidades de cache entre versões, variáveis de ambiente esquecidas, deploys sem plano de rollback — situações que geraram indisponibilidade e, inevitavelmente, calls de incidente fora de horário.
 
Este guia não nasceu de uma teoria. Nasceu da prática. Cada item do checklist reflete um problema real que já enfrentamos ou que sabemos que vai acontecer se não nos protegermos.
 
O objetivo é simples: garantir que nossas entregas sejam resilientes, seguras, e que nenhum desenvolvedor precise "apagar incêndios" fora de hora.
 
---
 
## O Protocolo de Confiança
 
Para que nosso processo de revisão seja eficiente e respeitoso com o tempo de todos, seguimos dois princípios:
 
### 1. O Checklist é o seu "Sinal Verde"
 
Antes de solicitar o review, faça o seu *Self-Review* e marque os itens do checklist. Se um revisor abrir um PR e o checklist estiver vazio, ele pode pedir que o autor valide os pontos antes de iniciar a revisão. Isso não é bloqueio — é respeito mútuo pelo tempo de cada um.
 
### 2. Contrato de Confiança
 
Encare as caixas de seleção como um compromisso entre você e o time.
 
- **Não é burocracia:** é proteção. Cada item existe porque alguém já sofreu a consequência de ignorá-lo.
- **Atenção ao marcar:** marcar um item sem realmente tê-lo validado quebra esse contrato e coloca em risco a estabilidade do sistema — e o seu próprio descanso *(ninguém quer ficar até as 00:00 em uma war room de incidente)*.
- **Nem tudo se aplica a todo PR:** se um item não é relevante para a sua alteração, marque como `~N/A~`. Um fix de typo no README não precisa de plano de rollback — e está tudo bem. O importante é que a decisão de pular um item seja consciente, não por preguiça.
 
---
 
## Detalhamento dos Itens
 
### Recursos AWS (SQS, BD, Dynamo, etc.)
 
| Item | Por que importa |
|---|---|
| **Provisionamento** | Garante que a aplicação não falhe ao tentar interagir com um recurso que não existe. Uma fila SQS ou tabela DynamoDB faltante pode derrubar toda uma feature silenciosamente. |
| **Acessos** | Evita que um serviço tenha permissões excessivas (como apagar uma fila), limitando o raio de explosão em caso de falha ou comprometimento de credenciais. |
| **Validação DevOps** | Garante que a configuração do recurso (DLQs, tempo de retenção, throughput) está otimizada para performance e custo. Uma fila sem DLQ, por exemplo, perde mensagens silenciosamente em caso de erro. |
 
### Configuração e Segurança
 
| Item | Por que importa |
|---|---|
| **Env Vars** | Evita o erro clássico de "funciona no staging mas quebra em produção" porque alguém esqueceu de cadastrar a variável. Valide nos três ambientes: Staging, Sandbox e Produção. |
| **Secrets** | Protege chaves de API e senhas. **Nunca** use texto plano no código ou em variáveis de ambiente comuns. Utilize o Secrets Manager. Um segredo vazado em um commit pode comprometer toda a infraestrutura. |
| **IAM** | Garante que as permissões de acesso seguem o princípio de menor privilégio. Cada serviço deve ter acesso apenas ao que precisa — nada mais. |
 
### Banco de Dados e Estado Compartilhado
 
Esta seção existe por causa de aprendizados reais. Já tivemos um incidente onde uma atualização de framework mudou o formato de serialização do cache no Redis. O deploy da nova versão funcionou, mas ao precisar fazer rollback, a versão anterior não conseguia ler o que a nova versão havia escrito. O resultado foi a necessidade de limpar o cache inteiro em produção.
 
| Item | Por que importa |
|---|---|
| **Migrações reversíveis** | Se o deploy falhar e você precisar fazer rollback do código, as migrações já executadas continuam no banco. Se uma migração removeu uma coluna que o código anterior ainda lê, o rollback do código vai quebrar a aplicação. A regra é: **migrações devem ser compatíveis com a versão atual e a anterior do código.** Na prática: prefira adicionar colunas (não remover), use valores default, e separe migrações destrutivas em deploys posteriores. |
| **Compatibilidade de formato** | Sempre que o PR altera como dados são serializados ou lidos de um armazenamento compartilhado (Redis, filas SQS/Sidekiq, sessões, cookies), é necessário garantir que a transição seja gradual. A abordagem segura é: primeiro faça o código novo **ler** os dois formatos (antigo e novo), mas continue **escrevendo** no formato antigo. Depois que o deploy estiver estável e sem necessidade de rollback, faça a mudança para escrita no formato novo em um segundo deploy. |
 
**Exemplos de situações que este item protege:**
- Atualização de versão do framework que muda o formato de serialização do cache (ex: Rails 7.0 → 7.1).
- Mudança no formato de payload de jobs em filas (Sidekiq, SQS). Jobs enfileirados pela versão nova que não conseguem ser processados pela versão antiga em caso de rollback.
- Alteração no formato de sessões armazenadas em Redis ou cookies, que pode deslogar todos os usuários em caso de incompatibilidade.
- Mudança em como dados são encriptados/decriptados (ex: `ActiveRecord::Encryption`), tornando registros ilegíveis para a versão anterior.
 
### Deploy e Resiliência
 
| Item | Por que importa |
|---|---|
| **Disponibilidade** | O autor é quem melhor conhece a alteração. Estar disponível durante o deploy reduz drasticamente o tempo de resposta a anomalias (MTTR). Não precisa ser o autor — pode ser um representante que conheça o contexto. |
| **Plano de Rollback** | É o nosso "paraquedas". Ter uma estratégia de reversão clara e documentada *antes* do deploy reduz o stress e o tempo de indisponibilidade em falhas críticas. Descreva o plano diretamente no PR — pode ser algo simples como "reverter para a tag anterior" ou algo mais elaborado se houver migrações ou mudanças de infraestrutura. |
| **Integrações** | Valida se o ecossistema (APIs externas, webhooks, bancos, serviços internos) está pronto para a nova versão. Uma API parceira que mudou o contrato, um webhook que agora espera um campo novo — falhas em cascata geralmente nascem aqui. |
 
### Qualidade e Testes
 
| Item | Por que importa |
|---|---|
| **Testes unitários/integração** | Código sem teste é código que não pode ser refatorado com segurança. O PR deve incluir testes que cubram o comportamento novo ou alterado. Se a base de código já tinha testes, confirme que eles continuam passando. |
| **Regressão** | Alterações em módulos compartilhados (libs internas, middlewares, serializers, services) têm potencial de impacto em várias partes do sistema. Se o PR toca nesses pontos, os testes de regressão dos módulos dependentes devem ser executados. |
| **Cenários de QA** | Os cenários de teste devem estar definidos e documentados *antes* da execução. "Executei os testes" sem saber quais cenários foram cobertos não dá visibilidade suficiente. Valide no ambiente de staging e registre os resultados. |
 
---
 
## Quando um item "Não se Aplica"
 
Nem todo PR exige todos os itens. Um fix de CSS não precisa de validação de IAM. Uma correção de typo não precisa de plano de rollback.
 
A orientação é:
 
- Se o item **não se aplica**, marque como `~N/A~` e escreva uma justificativa curta ao lado. Exemplo: `~N/A~ — alteração apenas visual, sem impacto em infra`.
- Se você está **em dúvida** se o item se aplica, ele provavelmente se aplica. Na dúvida, valide.
- O objetivo do checklist é fazer você **pensar** sobre cada ponto, não preencher formulário mecanicamente.
 
---
 
## Resumo Visual do Fluxo
 
```
Desenvolvimento concluído
        │
        ▼
  Self-Review + Checklist
  (marcar itens ou justificar N/A)
        │
        ▼
  Solicitar Code Review
        │
        ▼
  Reviewer valida checklist + código
        │
        ▼
  Aprovação + Merge
        │
        ▼
  Deploy assistido
  (autor ou representante disponível)
        │
        ▼
  Monitoramento pós-deploy
```
 
---
 
## Dúvidas e Contribuições
 
Este é um documento vivo. Se você identificar um cenário que deveria estar no checklist ou uma melhoria no processo, abra um PR neste próprio repositório ou traga na próxima retrospectiva. O guia evolui junto com o time.

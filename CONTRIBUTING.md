# Guia de contribuições

## Code Review

Revisar PRs é tão importante quanto escrever código: espalha conhecimento e melhora tanto o código quanto as pessoas que o escrevem. Revise com calma. Um ou dois PRs por dia rendem mais do que dez de uma vez, e acima de 500 linhas por hora a eficácia da revisão cai.

- **Um PR não precisa ser perfeito.** Se deixa a aplicação melhor do que estava, é um bom PR. Escolhas diferentes das suas não estão necessariamente erradas.
- **Quem desenvolve deve ter desapego.** Code review não é ataque pessoal, e sugestões simples podem ser acatadas mesmo com pequenas discordâncias.
- **Quem revisa deve justificar e sugerir.** Em vez de "Mude o nome desse método, não está bom", escreva "O nome poderia ser mais descritivo, que tal `novo_nome_mais_descritivo`?". Se possível, inclua um link ou um exemplo que explique a mudança.

### O que olhar

- **Testes**: o código novo tem testes? Se altera código existente, os testes atuais cobrem os novos casos?
- **Funcionalidade**: o código faz o que o autor pretende? A regra de negócio pode ter complicações que não conhecíamos.
- **Design**: o código está bem escrito, integra bem com o restante da base e usa nomes que fazem sentido no nosso domínio? Já existe algo parecido que possa ser reaproveitado?

Quando aplicável, olhe também:

- **Complexidade**: o código poderia ser mais simples?
- **Performance**: há algum problema óbvio, como uma query N+1?
- **Segurança**: há algum problema óbvio?

### O que **não** olhar

- **Guia de estilo**: os linters cuidam disso. Se não estiverem passando, o PR não será aceito
- **Testes quebrados**: corrigi-los é responsabilidade do autor, e não deve ser necessário apontar isso. PRs com testes falhando não serão aceitos

## Pull Requests

Para que o processo de code review e merge seja o mais eficiente possível, siga as diretrizes abaixo.

### Tamanho

Um bom PR é pequeno o bastante para ser revisado com qualidade e **autocontido**: faz sentido sozinho, sem depender de outros PRs.

Entregar só parte de uma funcionalidade é aceitável, desde que essa parte seja autocontida. Por exemplo, uma nova classe pode ser usada primeiro em uma parte da aplicação e, em outro PR, em outra.

PRs menores são revisados com mais qualidade, mergeados mais rápido e mais fáceis de reverter. Para agentes revisores, PRs grandes também consomem janela de contexto, custo e tempo.

#### Limites

O tamanho é medido por dois critérios: **linhas alteradas** (adicionadas e removidas) e **arquivos alterados**. O nível do PR é o **pior** dos dois.

| Nível | Linhas alteradas | Arquivos alterados | O que fazer |
|---|---|---|---|
| Ideal | menos de 300 | menos de 10 | Siga em frente |
| Alerta | de 300 a 800 | de 10 a 19 | Avalie se dá para dividir. Se não der, explique o motivo na descrição |
| Crítico | mais de 800 | 20 ou mais | Quebre o PR em PRs menores |

Exemplo: um PR com 250 linhas alteradas em 22 arquivos é **Crítico**, porque o critério de arquivos é o pior dos dois.

Existem exceções, como alterações em vários arquivos ao mesmo tempo para corrigir um problema de estilo. Nesses casos, explique na descrição do PR. Em geral, PRs nos níveis Alerta e Crítico devem ser raros.

### Regras

- PRs são abertos com base na `main`. A única exceção são os hotfixes, que seguem o fluxo de release (veja [Hotfix](#hotfix))
- Toda alteração na `main` é feita exclusivamente via PR
- Todo trabalho tem uma issue no Jira, inclusive ajustes pequenos (typo, dependência). Se ela não existir, crie antes de abrir a branch
- PRs sempre em português, tanto o título quanto a descrição
- O título do PR segue o padrão `[ISSUE] (tipo) Mensagem` (veja [Título do PR](#título-do-pr))
- O nome da branch segue o padrão `tipo/ISSUE[-descricao]` (veja [Convenção de nomes de branches](#convenção-de-nomes-de-branches))
- O tamanho do PR respeita os limites da seção [Tamanho](#tamanho)
- Garanta que os commits no PR são somente os necessários, e resolva qualquer problema de rebase (por exemplo, commits a mais que você não escreveu) antes de pedir review
- Se o PR ainda está em criação, deixe-o como *Draft* (veja [Trabalho temporário](#trabalho-temporário))
- A branch é removida automaticamente após o merge (opção *Automatically delete head branches* ativada no repositório)
- Branches sem atividade há mais de 30 dias são consideradas abandonadas: feche o PR ou retome o trabalho. Elas podem ser removidas sem aviso
- PRs só podem ser mergeados se todos os checks estiverem passando. Não podemos mergear PRs "vermelhos"
- O merge é feito via *squash merge*, usando o título do PR como mensagem do commit. Assim, o histórico da `main` fica com um commit por PR, no padrão `[ISSUE] (tipo) Mensagem`, pronto para gerar o Changelog

### Códigos de tipo

Todo código tem 6 letras minúsculas. O mesmo vocabulário vale para o prefixo da branch e para o `(tipo)` do título do PR.

| Código | Significado |
|---|---|
| `featur` | Nova funcionalidade |
| `bugfix` | Correção de bug: planejada, hotfix ou sustentação |
| `experi` | Experimento (sempre atrás de feature toggle e sem passar pelo QA) ou PoC (prova de conceito que não vai para produção e nunca é mergeada) |
| `devops` | Infra, pipelines e automações |
| `toggle` | Funcionalidade habilitada via feature toggle |
| `releas` | Release (apenas prefixo de branch, criada pela pipeline) |

Qualquer outro código é inválido (`story`, `feat`, `fix`, `hotfix`, `release`, `exp`, `poc`, `spike`, `temp`, `tmp`, `test`, `chore`, `refactor`, `docs`, etc.).

#### Qual tipo usar quando não é óbvio

| Trabalho | Tipo |
|---|---|
| Refatoração sem mudança de comportamento | `featur` |
| Apenas testes ou documentação | `featur` |
| Upgrade de dependência | `devops` |
| Remoção de feature toggle | `toggle` |
| Hotfix em produção | `bugfix` (contra a release) |

### Título do PR

Formato: `[ISSUE] (tipo) Mensagem`

- **ISSUE**: chave da issue no Jira, em maiúsculas. Ex.: `CPT-600`
- **tipo**: obrigatório. Um dos códigos da tabela acima, em minúsculas, exceto `releas`
- **Mensagem**: descrição curta, clara e objetiva, começando com verbo no presente. O título poderá ser usado para gerar o Changelog, então precisa ser legível e compreensível só de bater o olho

Exemplos:

- `[CPT-600] (featur) Cria checagem de cpfs em pades assinados`
- `[CPT-600] (bugfix) Corrige validação de CPFs em pades assinados`
- `[CPT-702] (toggle) Habilita suporte a novos tipos de documentos`

### Convenção de nomes de branches

Formato: `tipo/ISSUE[-descricao]`

- **tipo**: um dos códigos da tabela, em minúsculas
- **ISSUE**: chave do Jira em maiúsculas, igual ao Jira. Ex.: `QE-785`
- **descricao** (opcional): poucas palavras em *inglês*, minúsculas, separadas por hífen. Obrigatória quando a mesma issue tiver mais de uma branch

Exemplos: `featur/QE-785`, `featur/CPT-600-cpf-validation`, `featur/CPT-600-signed-pades`

> **Desvio intencional do [Conventional Branch](https://conventionalbranch.org/):** a spec exige tudo em minúsculas. Mantemos a chave do Jira em maiúsculas para que a integração GitHub ↔ Jira vincule a branch à issue. Por isso, nunca crie duas branches que diferem só em maiúsculas/minúsculas.

#### Regras de caracteres

- Apenas letras, números, `-` e `/` (um único `/`, separando tipo e restante)
- Sem espaços, `_`, acentos ou caracteres especiais
- Sem hífens consecutivos (`--`)
- A descrição não começa nem termina com `-`
- `.` só é permitido na versão de release (`releas/v1.4.2`)
- PRs Front pelo menos 2 aprovações para seguir com o merge
- PRs Back pelo menos 2 aprovações para seguir com o merge
- PRs que envolvam back e front pelo menos 4 aprovações para seguir com o merge, sendo 2 de back-end e 2 de front-end
- PRs que contenham inserção, alteração ou remoção de testes automatizados pelo menos 1 aprovação de QA para seguir com o merge.

| Prefixo | Padrão | PR aberto contra |
|---|---|---|
| `featur` | `featur/ISSUE[-descricao]` | `main` |
| `bugfix` | `bugfix/ISSUE[-descricao]` | `main`, ou a release no caso de hotfix |
| `experi` | `experi/ISSUE[-descricao]` | `main` (experimento) ou *Draft* até o fim (PoC) |
| `devops` | `devops/ISSUE[-descricao]` | `main` |
| `toggle` | `toggle/ISSUE[-descricao]` | `main` |
| `releas` | `releas/vMAJOR.MINOR.PATCH` | n/a (criada pela pipeline; a única criação manual permitida é a release de hotfix, veja [Hotfix](#hotfix)) |

Nomes sem `/`, sem a chave do Jira ou com prefixos fora da tabela não seguem o padrão.

#### Exceções

| Branch | Motivo |
|---|---|
| `main` | Branch troncal, sem prefixo |
| `dependabot/*`, `renovate/*` | Criadas por bots de dependência |
| `copilot/*` | Criada automaticamente pelo GitHub Copilot coding agent, sem opção de configurar o nome |

**Agentes de IA** que permitem escolher o nome da branch (Claude Code, Cursor, Copilot no editor etc.) seguem o padrão `tipo/ISSUE[-descricao]`. Não usamos prefixos como `claude/`, `cursor/` ou `ai/`. Para identificar trabalho feito por IA, use a tag `ai-generated` no PR. Configure o agente via `CLAUDE.md` / `.github/copilot-instructions.md`.

Nos PRs vindos de `copilot/*`, o título do PR continua obrigatoriamente no padrão `[ISSUE] (tipo) Mensagem`. Ajuste o título antes de pedir review.

#### Validação

O nome da branch e o título do PR são validados no CI. Fora do padrão, o PR não é aceito.

```
Branch:   ^(featur|bugfix|experi|devops|toggle)/[A-Z][A-Z0-9]+-[0-9]+(-[a-z0-9]+)*$
Release:  ^releas/v[0-9]+\.[0-9]+\.[0-9]+$
Exceções: ^(main|dependabot/.+|renovate/.+|copilot/.+)$
Título:   ^\[[A-Z][A-Z0-9]+-[0-9]+\] \((featur|bugfix|experi|devops|toggle)\) .+$
```

#### Hotfix

1. Crie `releas/v1.X.1` a partir da release que está em produção (ou da desejada)
2. A partir dela, crie `bugfix/ISSUE` e abra o PR contra essa release
3. Atualize o changelog manualmente, para que tenhamos o rastreamento de entrada do hotfix
4. Abra PRs em cascata para as demais releases fechadas e para a `main`, para que as próximas versões incorporem o hotfix

### Trabalho temporário

Não usamos branches `temp/` nem `tmp/`. Todo trabalho em andamento, teste ou tentativa que pode ser descartada deve ser feito em uma branch com prefixo válido (o que melhor descreve o trabalho) e com o PR aberto como *Draft*.

- O *Draft* sinaliza que o PR ainda não está pronto para review e impede o merge enquanto não for marcado como *Ready for review*
- Quando o trabalho estiver pronto, marque o PR como *Ready for review* e adicione a tag `ready to review`
- Se o trabalho for descartado, feche o PR e remova a branch

#### PoC

O PR de uma PoC (`experi/ISSUE`) permanece como *Draft* durante toda a sua vida e nunca é mergeado. Ele serve para revisão, discussão e registro do que foi aprendido. Ao fim da PoC, feche o PR (registrando a conclusão na descrição ou em um comentário) e remova a branch. Se a PoC for aprovada para ir adiante, abra um novo PR com o tipo adequado (`featur`, `toggle`, etc.) a partir de uma nova branch.

### Tags

- Coloque tags no PR que ajudem a filtrá-lo. Por exemplo: `front-end` se tiver mais a ver com front-end; a tag do squad, como `gerenciar`, se for de um squad específico; `feature toggle` se estiver só removendo feature toggles; etc.
- Se o PR foi feito com ajuda de um agente de IA, adicione a tag `ai-generated`
- Se o autor abriu um PR com os checks passando e pronto para review, adicione a tag `ready to review`
- Se o PR foi aprovado pelo número necessário de pessoas e está pronto para ser mergeado, adicione a tag `ready to merge`


## Convenções de nomes de arquivos

| Convenção | Exemplo | Onde é o padrão | Arquivos típicos |
|---|---|---|---|
| **kebab-case** | `pull-request-template.md` | Docs, web/front-end, URLs, CSS, configs, maioria dos projetos JS/TS | `user-profile.css`, `api-client.ts`, `getting-started.md`, `docker-compose.yml`, `plano-de-teste-login.md`, `login.cy.ts` |
| **snake_case** | `pull_request_template.md` | Python (PEP 8), Ruby/Rails, Go, SQL, Elixir. Templates do GitHub | `user_service.py`, `users_controller.rb`, `user_spec.rb`, `test_login.py`, `http_client.go`, `20261020_create_users.sql`, `.github/pull_request_template.md` |
| **camelCase** | `pullRequestTemplate.md` | Alguns arquivos JS/TS (hooks, utils, stores). Raro como padrão geral | `useAuth.ts`, `formatDate.js`, `authStore.ts`, `apiClient.js` |
| **PascalCase** | `PullRequestTemplate.vue` | Componentes Vue/React, classes Java/C#/Kotlin/Swift | `UserCard.vue`, `LoginForm.tsx`, `UserService.java`, `LoginPage.ts` (Page Object), `Program.cs` |
| **UPPER_CASE** | `README.md` | Arquivos "meta" do repositório e arquivos exigidos por ferramentas | `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `LICENSE`, `CODEOWNERS`, `.github/PULL_REQUEST_TEMPLATE.md` |
| **Nome fixo** | `Dockerfile` | Arquivos que a ferramenta procura pelo nome exato | `Dockerfile`, `Makefile`, `Gemfile`, `Rakefile`, `package.json`, `.gitlab-ci.yml`, `playwright.config.ts`, `cypress.config.js` |

> **Observação sobre `.github/`:** o GitHub aceita `pull_request_template.md` e `PULL_REQUEST_TEMPLATE.md` (não diferencia maiúsculas e minúsculas), mas **não** reconhece kebab-case nem camelCase. Os arquivos de workflow em `.github/workflows/` costumam usar kebab-case (`ci.yml`, `deploy-staging.yml`).

### Regras práticas de nomes de arquivos

1. **Arquivos com nome exigido por ferramenta:** use exatamente o nome esperado.
2. **Código:** siga a convenção da linguagem ou framework do repositório.
3. **Docs e arquivos soltos:** prefira **kebab-case**.
4. **Consistência acima de tudo:** não misture convenções dentro do mesmo repositório.
---

## Bibliografia

### Code Review

- Google Engineering Practices - <https://google.github.io/eng-practices/>
- How to Make Good Code Reviews Better - <https://stackoverflow.blog/2019/09/30/how-to-make-good-code-reviews-better/>
- Effective Code Reviews: Bettering Products, Teams, and Engineers - <https://medium.com/paypal-tech/effective-code-reviews-53d62a203b2f>
- Best Practices for Code Review (origem do limite de 500 linhas por hora) - <https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/>
- GitLab Code Review Guidelines - <https://docs.gitlab.com/development/code_review/>

### Branches

- Conventional Branch - <https://conventionalbranch.org/>

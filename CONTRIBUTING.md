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
- Todo o conteúdo textual do PR deve ser escrito em português, incluindo título, descrição e comentários
- O título do PR segue o padrão `[ISSUE] (tipo) Mensagem` (veja [Título do PR](#título-do-pr))
- O nome da branch segue o padrão `tipo/ISSUE[-descricao]` (veja [Convenção de nomes de branches](#convenção-de-nomes-de-branches))
- O tamanho do PR respeita os limites da seção [Tamanho](#tamanho)
- Garanta que os commits no PR são somente os necessários, e resolva qualquer problema de rebase (por exemplo, commits a mais que você não escreveu) antes de pedir review
- Se o PR ainda está em criação, deixe-o como *Draft* (veja [Trabalho temporário](#trabalho-temporário))
- A branch é removida automaticamente após o merge (opção *Automatically delete head branches* ativada no repositório)
- Branches sem atividade há mais de 30 dias são consideradas abandonadas: feche o PR ou retome o trabalho. Branches sem atividade há mais de 30 dias podem ser removidas sem aviso, exceto main, release/* e branches de PoCs com PR em Draft.
- PRs só podem ser mergeados se todos os checks estiverem passando. Não podemos mergear PRs "vermelhos"
- O merge é feito via *squash merge*, usando o título do PR como mensagem do commit. Assim, o histórico da `main` fica com um commit por PR, no padrão `[ISSUE] (tipo) Mensagem`, pronto para gerar o Changelog
- PRs de Front-end precisam de pelo menos 2 aprovações para seguir com o merge
- PRs de Back-end precisam de pelo menos 2 aprovações para seguir com o merge
- PRs que envolvam Back e Front precisam de pelo menos 4 aprovações para seguir com o merge, sendo 2 de Back-end e 2 de Front-end
- PRs que contenham inserção, alteração ou remoção de testes automatizados precisam de pelo menos 1 aprovação de QA para seguir com o merge

### Prefixos de branch

O [Conventional Branch](https://conventionalbranch.org/) (v1.1.0) define os prefixos `feature`, `bugfix`, `hotfix` e `release`, entre outros, e prevê `fix` como alias de `bugfix`. Usamos apenas `feature`, `fix` e `release` do padrão, mais `devops` como extensão própria.

Use sempre `feature` (e não o alias `feat`) para novas funcionalidades e `fix` para qualquer correção.

| Prefixo | Significado |
|---|---|
| `feature` | Nova funcionalidade, inclusive quando habilitada ou removida via feature toggle |
| `fix` | Correção de bug: planejada, sustentação ou urgente (une os antigos `bugfix` e `hotfix`) |
| `devops` | Infra, pipelines, automações e manutenção sem mudança funcional (absorve o antigo `chore`; extensão própria) |
| `release` | Release criada pela pipeline (a única criação manual permitida é para hotfix) |

Experimentos e PoCs também usam o prefixo `feature` (veja [PoC](#poc)).

O uso de IA não define prefixo. Branches criadas por agentes de IA usam o prefixo normal do trabalho (por exemplo, `feature`), e o PR é identificado pela tag `ai-generated` (veja [Tags](#tags)).

#### Qual tipo de PR usar quando não é óbvio

| Trabalho | Tipo |
|---|---|
| Refatoração sem mudança de comportamento | `feature` |
| Apenas testes ou documentação | `feature` |
| Upgrade de dependência | `devops` |
| Manutenção sem mudança funcional | `devops` |
| Remoção de feature toggle | `feature` (com a tag `toggle`) |
| Hotfix em produção | `fix` (contra a release) |

### Título do PR

Formato: `[ISSUE] (tipo) Mensagem`

- **ISSUE**: chave da issue no Jira, em maiúsculas. Ex.: `CPT-600`
- **tipo**: obrigatório. Um dos prefixos de trabalho listados na [tabela de prefixos de branch](#prefixos-de-branch), em minúsculas.
- **Mensagem**: descrição curta, clara e objetiva, em português e começando com verbo no presente. O título poderá ser usado para gerar o Changelog, então precisa ser legível e compreensível só de bater o olho

Exemplos:

- `[CPT-600] (feature) Cria checagem de cpfs em pades assinados`
- `[CPT-600] (fix) Corrige validação de CPFs em pades assinados`
- `[CPT-702] (feature) Habilita suporte a novos tipos de documentos`

### Convenção de nomes de branches

Formato: `tipo/ISSUE[-descricao]`

- **tipo**: um dos prefixos de trabalho listados na [tabela de prefixos de branch](#prefixos-de-branch), em minúsculas.
- **ISSUE**: chave do Jira em maiúsculas. Ex.: `QE-785`
- **descrição** (opcional): poucas palavras em *inglês*, minúsculas, separadas por hífen. Obrigatória quando a mesma issue tiver mais de uma branch

Exemplos: `feature/QE-785`, `feature/CPT-600-cpf-validation`, `fix/CPT-600-signed-pades`

> **Desvios intencionais do [Conventional Branch](https://conventionalbranch.org/) (v1.1.0):** `devops` é extensão própria. A chave do Jira na branch fica em maiúsculas para a integração GitHub ↔ Jira vincular a branch à issue, embora o padrão aceite apenas letras minúsculas nos nomes de branch. Os tipos do título do PR são uma convenção interna independente.

#### Convenção de commits

- Commits em pt-BR
- Commits no imperativo e presente
- Capitalizar primeiro caractere
- Manter commit coerente ao contexto do bloco commitado
- Manter boa descrição dos commits

#### Regras de caracteres

- Apenas letras, números, `-` e `/` (um único `/`, separando tipo e restante)
- Sem espaços, `_`, acentos ou caracteres especiais
- Sem hífens consecutivos (`--`)
- A descrição não começa nem termina com `-`
- `.` só é permitido na versão de release (por exemplo, `release/v1.2.0`)

| Prefixo | Padrão | PR aberto contra |
|---|---|---|
| `feature` | `feature/ISSUE[-descricao]` | `main` |
| `fix` | `fix/ISSUE[-descricao]` | `main`, ou a release no caso de hotfix |
| `devops` | `devops/ISSUE[-descricao]` | `main` |
| `release` | `release/vMAJOR.MINOR[.PATCH]` | n/a (criada pela pipeline) |

Nomes fora dos formatos da tabela não seguem o padrão, exceto as branches da seção [Exceções](#exceções).

#### Exceções

| Branch | Motivo |
|---|---|
| `main` | Branch troncal, sem prefixo |
| `dependabot/*` | Criadas pelo bot de dependências |

Agentes de IA devem seguir o padrão `tipo/ISSUE[-descricao]`. Configure o agente via `CLAUDE.md` / `.github/copilot-instructions.md`.

#### Validação

O nome da branch e o título do PR são validados no CI. Fora do padrão, o PR não é aceito.

```
Branch:   ^(feature|fix|devops)/[A-Z][A-Z0-9]+-[0-9]+(-[a-z0-9]+)*$
Release:  ^release/v[0-9]+\.[0-9]+(\.[0-9]+)?$
Exceções: ^(main|dependabot/.+)$
Título:   ^\[[A-Z][A-Z0-9]+-[0-9]+\] \((feature|fix|devops)\) .+$
```

#### Hotfix

1. Crie `release/vMAJOR.MINOR.PATCH` (por exemplo, `release/v1.2.0`) a partir da release que está em produção (ou da desejada)
2. A partir dela, crie `fix/ISSUE` (por exemplo, `fix/CPT-600`) e abra o PR contra essa release
3. Atualize o changelog manualmente, para que tenhamos o rastreamento de entrada do hotfix
4. Abra PRs em cascata para as demais releases fechadas e para a `main`, para que as próximas versões incorporem o hotfix

### Trabalho temporário

Não usamos branches `temp/` nem `tmp/`. Todo trabalho em andamento, teste ou tentativa que pode ser descartada deve ser feito em uma branch com prefixo válido (o que melhor descreve o trabalho) e com o PR aberto como *Draft*.

- O *Draft* sinaliza que o PR ainda não está pronto para review e impede o merge enquanto não for marcado como *Ready for review*
- Se o trabalho for descartado, feche o PR e remova a branch

#### PoC

Experimentos e PoCs usam o prefixo `feature`, no formato `feature/ISSUE[-descricao]` e com o tipo `(feature)` no título do PR.

O PR de uma PoC permanece como *Draft* durante toda a sua vida e nunca é mergeado. Ele serve para revisão, discussão e registro do que foi aprendido. Ao fim da PoC, feche o PR (registrando a conclusão na descrição ou em um comentário) e remova a branch. Se a PoC for aprovada para seguir adiante, abra um novo PR com o tipo adequado (`feature`, `fix` etc.) a partir de uma nova branch.

### Tags

Tags (labels) **categorizam** o PR para facilitar filtros e buscas. Elas **não são gatilho** de automações nem indicam o status do PR: os disparos usam os eventos nativos do GitHub, como a saída de *Draft* (*Ready for review*) e a mudança de branch base, e o status vem do próprio estado do PR (Draft, aprovações, checks).

#### Formato

- Sempre em **kebab-case**: minúsculas, palavras separadas por hífen
- Sem espaços, acentos, `ç`, dois-pontos (`:`), `~` ou outros caracteres especiais, que já causaram erro no CI
- Tags de um mesmo grupo compartilham um prefixo comum, para ficarem agrupadas na ordem alfabética

Exemplo: `team-ativacao` em vez de `team: VS ativação`.

#### Tags usadas

| Tag | Quando usar |
|---|---|
| `front-end` | O PR altera front-end |
| `back-end` | O PR altera back-end |
| `team-<nome>` | Time dono do PR, usada como filtro por revisores. Ex.: `team-ativacao`, `team-operador` |
| `toggle` | O PR habilita, altera ou remove feature toggles |
| `ai-generated` | O PR foi feito com ajuda de um agente de IA |

- PRs híbridos levam as duas tags, `front-end` e `back-end`. Não existe tag `full-stack`
- A tag de time usa um nome genérico (`team-`), e não o nome da VS ou do squad, que pode mudar

## Convenções de nomes de arquivos

Adotamos **kebab-case** como padrão para nomes de arquivo. As demais convenções só se aplicam quando uma ferramenta ou a linguagem do repositório exigem (veja [Regras práticas](#regras-práticas-de-nomes-de-arquivos)).

| Convenção | Exemplo | Onde é o padrão | Arquivos típicos |
|---|---|---|---|
| **kebab-case** | `pull-request-template.md` | Docs, web/front-end, URLs, CSS, configs, maioria dos projetos JS/TS | `user-profile.css`, `api-client.ts`, `getting-started.md`, `docker-compose.yml`, `plano-de-teste-login.md`, `login.cy.ts` |
| **snake_case** | `pull_request_template.md` | Python (PEP 8), Ruby/Rails, Go, SQL, Elixir. Templates do GitHub | `user_service.py`, `users_controller.rb`, `user_spec.rb`, `test_login.py`, `http_client.go`, `20261020_create_users.sql`, `.github/pull_request_template.md` |
| **camelCase** | `pullRequestTemplate.md` | Alguns arquivos JS/TS (hooks, utils, stores). Raro como padrão geral | `useAuth.ts`, `formatDate.js`, `authStore.ts`, `apiClient.js` |
| **PascalCase** | `PullRequestTemplate.vue` | Componentes Vue/React, classes Java/C#/Kotlin/Swift | `UserCard.vue`, `LoginForm.tsx`, `UserService.java`, `LoginPage.ts` (Page Object), `Program.cs` |
| **CONSTANT_CASE** | `PULL_REQUEST_TEMPLATE.md` | Arquivos "meta" do repositório e arquivos exigidos por ferramentas | `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `LICENSE`, `CODEOWNERS`, `.github/PULL_REQUEST_TEMPLATE.md` |
| **Nome fixo** | `Dockerfile` | Arquivos que a ferramenta procura pelo nome exato | `Dockerfile`, `Makefile`, `Gemfile`, `Rakefile`, `package.json`, `playwright.config.ts`, `cypress.config.js` |

> **Observação sobre `.github/`:** o GitHub aceita `pull_request_template.md` e `PULL_REQUEST_TEMPLATE.md` (não diferencia maiúsculas e minúsculas), mas **não** reconhece kebab-case nem camelCase. Os arquivos de workflow em `.github/workflows/` costumam usar kebab-case (`ci.yml`, `deploy-staging.yml`).

### Regras práticas de nomes de arquivos

1. **Arquivos com nome exigido por ferramenta:** use exatamente o nome esperado.
2. **Código:** siga a convenção da linguagem ou framework do repositório.
3. **Demais arquivos (docs, configs, arquivos soltos):** use **kebab-case**.
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

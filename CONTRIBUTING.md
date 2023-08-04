# Guia de contribuições


## Code Review

Revisão dos PRs é tão importante quanto fazer código. Com ela criamos uma coesão no time, onde todos estão trabalhando juntos para a melhoria não somente do código, mas da pessoa que desenvolve em si. Sempre aprendemos algo novo quando fazemos code review, e ninguém faz código tão perfeito que não esquece de um detalhe ou outro. Não precisa correr com a revisão, nem fazer dela sua única tarefa do dia. Estudos mostram que revisar mais de 500 linhas por hora diminui a eficácia da revisão, então pegar um ou dois PRs por dia e revisar com calma é melhor que revisar 10.

Importante lembrar que um PR não precisa ser perfeito para ser aceito. Se ele deixa a aplicação num estado melhor do que estava antes consideramos isso um bom PR. Sempre dá para melhorar uma coisa ou outra, mas chega um ponto onde os benefícios de trocar uma variável por outra não compensam o tempo gasto. Lembre que outras pessoas podem tomar decisões diferentes da sua, mas não significa que está errado: não é o objetivo fazer com que todo mundo codifique exatamente do mesmo jeito.

Da mesma forma a pessoa que desenvolveu o PR deve praticar um certo desapego com o código. Code review não é um ataque pessoal, sugestões simples que não afetam o andamento do código podem ser acatadas mesmo que discordemos um pouco.

O revisor deve sempre tentar embasar a razão para qual está pedindo uma mudança num PR e dar sugestões para possíveis modificações. Quando não temos um feedback construtivo podemos ficar frustrados. Por exemplo, não escreva somente "Mude o nome desse método, não está bom", mas escreva "O nome do método poderia ser mais descritivo, que tal chamar de `novo_nome_mais_descritivo`?". Também adicione algum link ou outros exemplo de código para explicar porque acha melhor a modificação, isso faz com que o revisor tenha que pesquisar a razão para modificação e com que a pessoa que desenvolveu o PR aprenda com essa pesquisa. Ganhamos dos dois lados.

É importante sabermos o que olhar e não olhar quando fazemos code review:

### O que olhar

* **Testes**: o código novo tem testes? Se está alterando um código já existe, os testes atuais cobrem os novos casos?
* **Funcionalidade**: o código faz o que o autor pretende? As vezes a regra de negócio tem algumas complicações a mais que não sabíamos quando escrevemos o código
* **Design**: o código tem um bom design? Isso é, o código é bem escrito e integra bem com o resto da base? Os nomes das classes fazem sentido para o nosso domínio? Talvez exista uma parte do código que já faça algo similar e esse novo código poderia contemplar ela?

Nem todos PRs terão todos esses seguintes problemas, mas também devemos ver:

* **Complexidade**: o código poderia ser mais simples?
* **Performance**: o código adiciona algum problema de performance óbvio? Por exemplo, uma query N+1?
* **Segurança**: o código tem algum problema de segurança óbvio?

### O que **não** olhar

* **Style guide**: temos linters para isso, se eles não estão passando o PR não será aceito
* **Testes quebrados**: se os testes estão quebrados é responsabilidade do autor corrigir, não deve ser necessário apontar isso. PRs onde testes não passam não serão aceitos

## Pull Requests

Para que o processo de code review e merge seja o mais eficiente possível, temos abaixo algumas diretrizes sobre PRs.

### Tamanho

Tente fazer com que seu PR seja pequeno o suficiente para facilitar o code review, mas grande o suficiente para que seja coerente. O tamanho do PR é algo subjetivo, mas o que não queremos são: PRs muito grandes onde o revisor desiste de revisar por demorar muito tempo; PRs muito pequenos onde ele depende de outros 3 PRs para comprir com o seu objetivo.

Não tem problema o PR entregar somente parte de uma funcionalidade. Por exemplo: uma refatoração onde adicionamos uma nova classe pode começar por ser usada em uma parte da aplicação, e depois em outro PR usamos essa nova classe em outra parte. O importante é que o PR seja auto-contido.

Tente levar em consideração que quanto menor um PR mais fácil de revisar ele com qualidade, o que o torna mais rápido mergear, e mais fácil de fazer rollback se necessário.

Existe algumas exceções, por exemplo quando modificamos vários arquivos ao mesmo tempo para arrumar algum problema de estilo. Mas em geral deveria ser raro PRs muito grandes.

### Regras

* PRs são abertos com base no branch `main` (salvo raras exceções)
* Não removemos coluna no banco de dados na mesma migração que removemos o código (salvo raras exceções)
  * Isso permite que seja feito rollback da release em um eventual problema de sustentação
* Não manipulamos registros do banco de dados em migrações, utilize tasks para esses cenários
  * Após a conclusão da abordagem crie um novo PR para excluir a task
* PRs sempre em português, tanto o título quanto descrição
* O PR deve ter no nome o card que ele resolve em colchetes no título: `[KZ12345] Refatoração da funcionalidade XXX`
* Coloque um título descritivo no PR, mas não muito longo. Esse título poderá ser utilizado para gerar o Changelog, então é importante que seja legível e compreensível só de bater o olho
* Garanta que os commits no PR são somente os necessários, e resolva qualquer problema de rebase (por exemplo, commits a mais que você não escreveu) antes de pedir review
* Se o PR ainda está em criação, deixe-o como *Draft*
* Sempre remova o branch depois que o PR for mergeado. Se você tem branches antigos que estão "stale" pode removê-los no Github
* PRs só podem ser mergeados se todos os checks estiverem passando. Não podemos mergear PRs que estiverem "vermelhos"

#### Convenção de nomes de branches

Nomes dos branches devem ser sempre em *inglês*. Usamos 4 tipos de branches com as seguintes convenções:

* **Hotfix**: quando for um bug em produção de caratér urgente. Convenção: `hotfix/{card number}`
* **Fix**: quando houver fix para histórias planejadas. Convenção: `fix/{card number}`
* **Release**: abertura de branch release. Convenção: `release/v1.1.1`
* **Feature**: resto dos casos. Convenção: `feature/{card number}`

O `{card number}` é o número do card proveniente do Kanbanize, com o prefixo `kz`, por exemplo: `feature/kz12345`.

#### Convenção de commits

- Commits em pt-BR
- Commits no imperativo e presente
- Capitalizar primeiro caractere
- Manter commit coerente ao contexto do bloco commitado
- Manter boa descrição dos commits

Exemplos de commit:
**"Adiciona nova função para deletar signatário do documento"**

### Fluxo de revisão

- PRs Front pelo menos 2 aprovações para seguir com o merge
- PRs Back pelo menos 2 aprovações para seguir com o merge
- PRs que envolvam back e front pelo menos 4 aprovações para seguir com o merge, sendo 2 de back-end e 2 de front-end

### Tags

* Coloque tags no PR que ajudem a filtrá-lo, por exemplo se tiver mais a ver com front end, a tag `front-end`; se for de um Squad específico pode por a tag do squad, e.g. `gerenciar`; se está só removendo feature toggles, `feature toggle`; etc.
* Se o/a autor/a abriu um PR e este está com os checks passando e pronto para review, adicione a tag `ready to review`
* Se o PR foi aprovado pelo número necessário de pessoas e está pronto para ser mergeado, adicione a tag `ready to merge`


----
## Bibliografia

* Google Engineering Practices - https://google.github.io/eng-practices/
* How to Make Good Code Reviews Better - https://stackoverflow.blog/2019/09/30/how-to-make-good-code-reviews-better/
* Effective Code Reviews - https://medium.com/paypal-tech/effective-code-reviews-53d62a203b2f
* Best Practices for Code Review - https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/
* Gitlab Code Review Guidelines - https://docs.gitlab.com/ee/development/code_review.html

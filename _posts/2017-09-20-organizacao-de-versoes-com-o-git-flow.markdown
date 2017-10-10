---
layout: post
date: 2017-09-20T19:57:23-03:00
title: "Organização de versões com o Git Flow"
author: cesaraugusto
abstract:  >
  Um passo a passo para iniciar o uso desse ótimo plugin, que facilita muito a nossa organização
---

Existem diversas estratégias de organização de branches, e para o cliente eMed-br quando passamos a trabalhar com Git, herdamos uma estratégia típica do Svn que era utilizado anteriormente.

Essa estratégia é basicamente a seguinte:

Temos uma branche chamada master, e a cada nova release criamos uma nova branche com o nome da versão. Essa versão normalmente tem um tempo de desenvolvimento de três meses, e durante esse período todos os desenvolvedores fazem seus commits nesta branche.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/0.png" />
</center>

Deste modo sempre funcionou muito bem, porém o cliente nos apontou duas mudanças na forma de trabalharmos. Primeiramente você precisa saber que a eMed-br vende o sistema para unimeds e hospitais, sendo assim, temos na pratica o sistema rodando em diversos clientes.
As mudanças que devem afetar a forma que trabalhamos com o versionamento do nosso código são as seguintes:

1.	Além da versão principal liberada a cada três meses, podem existir customizações liberadas antes disso, para algum cliente que tenha contratado. Essa customização deve ser liberada assim que finalizada, e unida a versão principal quando esta entrar em produção para todos os clientes.
2.	Possivelmente serão feitas alterações, principalmente hot-fix por uma equipe separada de suporte.
Tendo em vista este cenário, trago aqui uma alternativa para ajudar a padronizar tudo, de forma simples.

### Git Flow
O Git-Flow é um plugin para o git, que trata-se de um modelo de organização de branches e tags, estabelecendo uma nomenclatura bem estruturada para as mesmas. Basicamente as branches ficam assim:
- Branch master
- Branches release
- Branch develop
- Branches feature
- Branches hotfix
- Branches suport

### Mãos à obra
No meu caso, como já temos um projeto, criei para teste um projeto no git, já com a branche master para testar.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/1.png" />
</center>

No cygwin, já tendo o git instalado no Windows, e tendo suporte a wget

	wget -q -O - --no-check-certificate https://github.com/nvie/gitflow/raw/develop/contrib/gitflow-installer.sh | bash

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/2.png" />
</center>

Baixando localmente meu projeto do git

	git clone https://git.cwi.com.br/cesaraugusto/projeto-git-flow.git

Agora o comando para deixar o git flow trabalhar. Será pedido os nomes padrão das branches master, develop e demais, mas recomendo deixar tudo padrão, dando apenas enter em cada um dos passos.

	git flow init

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/3.png" />
</center>

Iniciando uma release. Isso será como vou utilizar para iniciar a branche principal de cada versão. Esta vai durar em torno de três meses em desenvolvimento antes de se juntar a develop.

	git flow feature start 2.5.13

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/4.png" />
</center>

Agora vamos criar um arquivo de teste, adicionar e commitar. Comandos padrão do git...

	nano recurso.txt
    git add .
    git commit -m "Commitando na feature 2.5.13"

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/5.png" />
</center>

Agora podemos mandar para o nosso git remoto esta alteração

	git flow feature publish 2.5.13

Neste ponto a branche aparece no gitlab

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/6.png" />
</center>

Quando o desenvolvimento de uma feature termina, podemos utilizar o seguinte comando

	git flow feature finish 2.5.13

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/7.png" />
</center>

Como neste exemplo ainda não tínhamos publicado a branche develop, e o git flow nos direcionou para ela, vamos aproveitar para envia-la para o gitlab

	git push --set-upstream origin develop

E lá agora temos o seguinte

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/8.png" />
</center>

Para testar a questão de customizações extras, vou adicionar mais uma feature e finaliza-la

	git flow feature start customizacao-amr
    git flow feature publish customizacao-amr
    git flow feature finish customizacao-amr

Agora terminamos o desenvolvimento de uma versão, e vamos lançar uma release do sistema.

	git flow release start 2.5.13

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/9.png" />
</center>

Despois de homologada, podemos juntar tudo na master

	git flow release finish 2.5.13

Agora vamos ver como ficaram nossas branches e tags ao final desse processo

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-20-organizacao-de-versoes-com-o-git-flow/10.png" />
</center>

### Conclusão
O git flow se mostrou bastante prático e simples de ser utilizado. Acredito que tenhamos um bom ganho na organização do nosso projeto, tendo atendido às novas necessidades do cliente.

Tudo isso ainda está em fase de implantação, e vamos ver ainda ao longo do tempo se podemos utilizar o sistema com seus defaults, ou se precisamos fazer algumas adaptações.

### Referências
- https://fjorgemota.com/git-flow-uma-forma-legal-de-organizar-repositorios-git/
- https://github.com/nvie/gitflow/wiki/Windows
- https://github.com/nvie/gitflow
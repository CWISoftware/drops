---
layout: post
date: 2016-06-20pT12:00:00-02:00
title: "Dormindo a noite após um deploy em produção"
author: giovani-barili
abstract: >
  Estratégias e técnicas para garantir um *rollout* e *rollback* de ambientes produtivos e ainda continuar dormindo a noite sem preocupações!
---

Dentro das atividades de desenvolvimento de software, umas delas e que está associado a um grande risco é o planejamento e a realização de liberações/*deploy* de novas versões, funcionalidades ou correções em produção. Isso se deve ao fato de que qualquer erro pode paralisar um ambiente produtivo por minutos, horas ou até mesmo dias. Onde geralmente esses *downtime* (tempo de inatividade) acabam gerando uma perda financeira, pelo fato de não permitir a venda, contratação, entre outros, ou até mesmo gerando um sentimento de desconfiança não permitindo a conversão de novos clientes para a plataforma ou sistema. Por esse motivo, o processo de deploy em geral exige um plano de *rollout* (plano de implementação) e ele seja realizado em ambientes de homologação, staging, entre outros antes de chegar em produção, sempre contanto com um plano de *rollback* (retorno) em caso de falhas.

No entanto, apesar de planejado, testado e re-testado, muitos *deploys* acabam apresentando problemas e exigindo um *rollback* imediato ou após a notificação de instabilidade do sistema por parte dos usuários. Pois dentro desse processo existem diversas variáveis que nem sempre são esperadas ou que se esperadas que não afetem o deploy em produção. Entre elas podemos citar:

- Volume de dados/usuários do sistema, que é diferente entre os ambientes
- Bases de dados entre ambientes que apresentam estrutura de dados diferentes
- Sistemas terceiros com versões;
- Configurações ou instalações de aplicações ou SDK;

## DevOps

Aonde não estamos contanto com todo envolvimento necessário das pessoas que irão atuar no *deploy*, como os responsáveis pelo desenvolvimento e os responsáveis pela infraestrutura ou operação dos sistemas em produção. Onde tornasse necessário uma troca de informações, sincronismos e integração para que todo o processo ocorra da forma mais eficiência e com o menor risco possível. Começando a falar nesse ponto <img src="http://www.iconarchive.com/download/i103481/paomedia/small-n-flat/sign-warning.ico" style="width:20px"/>, começamos a falar de uma das grandes dificuldades do desenvolvimento do software que é a iteração **dev** x **ops**. Isso se deve ao fato que a equipe de desenvolvimento está focada em construir o sistema e liberá-lo no prazo, já a equipe de operação está focada em garantir que todos os sistemas produtivos estejam 100% operacionais e os mesmos não necessitem ser acionados na madrugada e/ou feriados devido a problemas de implantações ou de sistemas. No fim, o operacional sempre tem que ajudar juntamente para resolver os problemas de produção!

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/devfine.jpg" />
</center>

Muitos dos problemas de produção ocorrem devido as situações não esperadas entre ambientes, falhas de desenvolvimento e o próprio erro humano na execução dos *deploy*. Umas das formas de minimizar esse problema é utilizando um processo de integração contínua, onde após configurada, o processo de deploy é executado de forma idêntica em cada deploy, conforme viemos apresentando em nossos [Drops](http://cwisoftware.github.io/drops/integracao-continua-e-deploy-continuo-tempo-e-dinheiro) e [Dojos](https://www.youtube.com/watch?v=2SaAVVTF_oQ). Mas mesmo tendo um processo totalmente integrado ainda não temos a garantia de um deploy sem falhas, e para isso contamos com estratégias e técnicas para evitar o *downtime* do sistema, e em caso de falho permitir um *rollback* sem medo e da forma mais ágil possível!

## Banco de dados inerente a versão

Para conseguirmos trabalhar com as estratégias de *deploy* de iremos apresentar, umas das premissas é conseguirmos manter duas verões do mesmo sistema em cima do mesmo ambiente. Ou seja, conseguirmos ter a versão atual, ex. 1, e uma versão nova, ex. 2, acessando a mesma base de dados, repositórios de arquivos entre outros. Para que seja possível realizar um *rollout* e um *rollback* com um impacto imperceptível!

Por exemplo, dado que haja uma versão **v1** em produção e vamos liberar uma versão **v2**. Nesse caso, a atualização da base de dados para a **v2** não pode fazer com que a **v1** pare de funcionar. Para isso temos que nos atentar:

- Que as novas colunas aceitem nesse primeiro momento valores nulos;
- Tenham algum valor *default*;
- Sejam preenchidos por *trigger*;
- Atualizam de *procedures* saibam trabalharam com essas situações;
- Entre outras abordagens;

Com isso, caso a atualização do sistema de **v1** para **v2** tenha problemas, podemos retornar para a **v1** com menores impactos! Após a liberação e validação da **v2** em produção, podemos ter outro script de banco de dados que ajuste essas tabelas/*triggers*/*procedures* para serem mais consistentes com o modelo de dados atual, exigindo o preenchimento dos campos até então nulos.

Em alguns casos, talvez seja necessário gerar uma versão intermediária do sistema, para que possa receber uma atualização de base de dados e não tenha impactos, conforme apresentado na imagem abaixo, quando é necessário criar uma **v2.1** para poder atualizar a base de dados para a versão **v3**.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/basededados.gif" />
</center>

## Sistema distribuído

Outra questão importante é que o sistema as vezes tem que ser capaz de trabalhar de forma distribuída, ou adaptado para que os dados dos usuários sejam inerentes ao servidor que ele acesse. Em algumas estratégias de *deploy*, o acesso do usuário em um servidor **x** ou **y** pode se dar de forma aleatória ou de forma programada. Um dos maiores problemas ocorre quando dados de autenticação, permissão entre outros são armazenados na sessão do servidor de aplicação, assim, quando o usuário é direcionado para um servidor que não possua tais dados é retornando um erro de falha ou de não autenticado.

Uma das possíveis solução é armazenar tais dados em uma ferramenta de *Memory Cache*, por exemplo [Redis](http://redis.io/), [MemCached](https://memcached.org/). Muitos dos servidores de aplicação, atualmente já possuem integração direta com tais ferramentas.

## Pré-Produção
   
A estratégica de pré-produção consiste em construir um ambiente paralelo ao ambiente produtivo, acessando a mesma base de dados, diretórios entre outros, no entanto com uma versão mais atual e que o acesso a esse ambiente seja controlado. Para a configuração dessa ambiente, muitas vezes um subdomínio já é capaz de resolver, o qual será disponibilizar para o(s) usuário(s) desse ambiente, conforme imagem abaixo.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/preprod.png" />
</center>

Essa á uma das estratégias mais básica, e com menor esforço para implantação. Onde em caso de falha, a orientação aos usuários desse ambiente é que retornem ao ambiente padrão, visto que todos os dados incluídos, alterados no ambiente pré-produtivo também afetaram o ambiente produtivo. Não havendo perda de produção nem *downtime* por parte dos usuários, e sem a necessidade de uma intervenção emergencial ou acionamento das equipes de desenvolvimento ou operação.

## *Blue-Green*

A estratégia de *deploy Blue-Green* é um segundo nível para quem já possuí um ambiente pré-produtivo, onde consiste em uma forma chaveada da utilização do ambiente com a versão atual (*Green*) e a versão nova (*Blue*). Tal chaveamento pode ser realizado por meio da alteração de IP do domínio de DNS, ou por configuração de um *Load Balancer*, conforme apresentado na imagem abaixo. Vale a pena ressaltar que a abordagem com alteração do DNS possui um delay, devido a necessidade de atualização do IP nas máquinas clientes.

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/bluegreen.gif" />
</center>

Nessa estratégia, inicialmente todos usuários estão acessando o ambiente *Green* e após a implantação da nova versão *Blue* todos os usuários são direcionados para o ambiente atualizado. Em caso de falha, o ambiente *Blue* passa para *Red* e todos usuários são redirecionados novamente para o ambiente *Green*, em caso de sucesso, o ambiente *Blue* passa a ser considerado *Green* e os usuários continuam acessando tal ambiente até novo *deploy*.

## *Canary*

A estratégia de *Canary deploy* é um nível mais avançado do *Blue-Green* que consiste em fazer uma migração dos usuários do ambiente *Green* para o ambiente *Blue*, permitindo uma avaliação de qualidade, performance entre outros durante cada iteração da migração. Nessa situação é necessário uma configuração mais avançando necessitando uma [DNS *Round-Robin*](https://en.wikipedia.org/wiki/Round-robin_DNS) ou um *Load Balancer* que permita um ajuste na forma de balanceamento, conforme apresentado na imagem abaixo.

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/canary.gif" />
</center>

Nessa estratégia, caso durante a migração seja identificado algum problema no sistema, de performance, entre outros pode-se suspender a migração para uma análise ou até mesmo retornar todos usuários do ambiente *Blue* para o ambiente *Green*, permitindo a reimplantação, correção e posteriormente retomar o processo de deploy.

## *Canary* e *Blue-Green* em ambientes *Cloud*

Muitos servidores de *Cloud* como [AWS](http://aws.amazon.com/) ou [Azure](https://azure.microsoft.com/pt-br/) oferecem serviços de *Load Balancer* onde configuramos uma quantidade mínima de servidores e em caso de falha/queda de um dos servidores ou na necessidade do escalonamento de novos servidores, eles sejam baseados em uma imagem pré-configurada. Com isso, durante a atualização podemos definir que a imagem inicial é *Green*, e na necessidade de atualização, configuramos o *Load Balancer* para se basear em uma imagem *Blue*. Assim, podemos ir derrubando servidores *Green* e avaliado a situação do sistema, até que todos servidores *Green* tenham sido atualizados para *Blue*, conforme imagem abaixo.

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/canary_bluegreen_cloud.gif" />
</center>

Nessa estratégia, em caso de falha, temos que reconfigurar o *Load Balancer* para se basear novamente na imagem *Green* e derrubar de forma total ou incremental os servidores *Blue*, para que os servidores *Green* sejam criados.

## Exemplos

Um dos maiores exemplos dessas estratégias de deploy é o **Facebook**, o qual é baseado em *Canary*. Quem nunca viu seu amigo com novas funcionalidades e você não conseguia encontrá-las no seu Facebook?!?! Isso se deve que novas versões são inicialmente disponibilizada para os funcionários do Facebook, depois para um pequeno grupo de usuários e de forma gradativa para os demais usuários, conforme imagem abaixo.

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/facebook.png" />
</center>

Outro exemplo, era a forma de *deploy* da **Netflix** em 2013, que era baseado em   um híbrido *Canary* e '*Red-Black*', mas atualmente o *deploy* deles estão totalmente baseado no *Canary*. Conforme imagem apresentada abaixo, em uma primeira fase o Netflix realizava uma *Canary* em uma de suas regiões e após a migração 100% desse ambiente e com avaliação total da versão eles começavam o processo de *Red-Black* nas demais regiões, até que todas estivessem *Red*

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/netflix.png" />
</center>

## Conclusão

Com a utilização dessas estratégicas e técnicas podemos considerar que ganhamos uma certa tranquilidade nos *deploy* visto que o processo se torna mais controlado e de simples alteração tanto para o *rollout* como para o *rollback*. Algumas delas são consideradas mais simples e de fácil implantação, outras nem tanto, e isso poderá depender de sistema para sistema, de projeto para projeto. O que foi apresentado aqui são abordagens, ideias, não significa que exista uma forma certou ou errada e sim o que funciona e o que não funciona no seu cenário, o importante é conseguir construir uma abordagem que dê mais confiança e conforto a cada liberação! 

<center>
  <img style="width:100%; margin: 10px" src="{{ site.baseurl }}/content/2016-06-20-dormindo-a-noite-apos-um-deploy/certo_errado.png" />
</center>

Possuí outras práticas, técnicas, compartilhe ela conosco!

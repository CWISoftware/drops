---
layout: post
date: 2016-04-13T12:00:00-02:00
title: "Integração Contínua e Deploy Contínuo, tempo é dinheiro"
author: giovani-barili
abstract: >
  Porque e como integração/deploy contínuo podem trazer lucros!
---

Em diversos projetos sempre estamos realizando processos manuais, mas de forma "automática", onde nem sempre percebemos a quantidade de esforço gasto nesses processos. Vamos tentar colocar na ponta do lápis. Se eu gasto 30 minutos por deploy de cada versão, sendo que em um final de sprint com correções de bugs e mudanças de última hora deva fazer uns 6 deploy, já estamos considerando 3 horas somente gastos em deploy. Sem contar erros humanos, configurações erradas, arquivos errados que só fazem aumentar esse tempo. Se formos levar em consideração em um projeto de 5 sprints, já estamos falando em 15 horas para deploy.

Se analisarmos, dentro do processo de desenvolvimento trabalhamos com um processo cíclico que parte do desenvolvimento, build, deploy, operação, melhorias e retornando ao desenvolvimento. Dentro de um projeto esse ciclo ocorre apenas entre as liberações, mas pensando em um cenário de fábrica ou operação esse ciclo ocorrer inúmeras vezes, conforme imagem abaixo.

<center>
  <img src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/cicd.jpg" />
</center>

Mas como esse processo pode gastar ou dar lucro? Vamos pegar como exemplos alguns cenários e imaginar o tempos e esforço gasto, $$$, com build e deploy, como exemplo: 

- [GitHub](https://github.com/blog/1241-deploying-at-github), de Janeiro a Setembro de 2012 foram realizados 41.679 builds e 12.602 deploys sendo somente no dia 23 de agosto de 2012 realizados 563 builds e 175 deploys.
- A Amazon uma das maiores varejistas do mundo com serviços de cloud realiza o [deploy de um novo software a cada segundo](http://www.zdnet.com/article/how-amazon-handles-a-new-software-deployment-every-second/). 
- O Facebook, visando o tamanho e o impacto que ele oferece atualmente, possui um [departamento de release engineering desde 2008](https://www.facebook.com/note.php?note_id=10150660826788920) com o objetivo de realizar uma liberação por dia como melhorias e novas funcionalidades. Com o novo escritório de engenharia em Londres, tinha como objetivo dobrar o número de deploys.

Apesar do Facebook ser apenas 1 ou 2 deploys por dia, como mencionado no artigo da Amazon, o deploy deles não é apenas um acesso SSH, atualização de base e troca de alguns arquivos. O processo deles engloba um número muito maior de processos e variáveis. A [Netflix](http://techblog.netflix.com/2013/08/deploying-netflix-api.html) apresenta algumas técnicas e abordagens de deploy deles em 2013.

Mas vamos considerar um cenário mais real, não pensando em mega aplicações, de escala global com milhões de usuários. Em um projeto meu, hoje foram realizados 17 deploys, considerando um sprint de 10 dias são 170 deploys. Quanto tempo eu gastei. Umas 8 horas no primeiro dia projeto e nada mais! Aí vem a questão, alguns dos cálculos está errado ou tem alguém fazendo isso para mim.

<center>
  <img style="float: left; width: 300px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/jenkins.png" />
</center>

Uma das ferramentas que realizam esse trabalho e uma das mais famosa é o [Jenkis](https://jenkins.io/). Como o próprio logo já demonstra ele é um mordomo, responsável por realizar as atividades/comandos que lhe são atribuídos. Teve sua origem com o nome Hudson, iniciado por equipes da Sun. Mas com a compra da Sun pela Oracle, seus criadores resolveram torná-lo totalmente OpenSource, alterando seu logo e seu nome. Umas das grandes vantagens dessa ferramenta e de estar sendo desenvolvida por uma comunidade muito ativa, com novas versão semanalmente e uma infinidade de plug-ins que são auto instaláveis via o menu de plug-ins do próprio Jenkins.

A instalação do Jenkins é extremamente básica, no caso do [Windows](https://jenkins.io/content/thank-you-downloading-windows-installer#stable) atualmente é um instalador do tipo next, next, next, e para ambientes [Linux](http://pkg.jenkins-ci.org/debian-stable/) é um .war onde pode ser executado e posteriormente configurado como serviço. Após a sua instalação ele estará disponível no http://localhost:8080.

O Jenkins no meu caso é muito mais uma escolha por gosto, e afinidade. Na minha opinião o Jenkins é mais flexível para fazer inúmeros passos nos processos que trabalho. No entanto existem diversas outras ferramentas que podem ser utilizadas para integração contínua, como o TeamCity, Snap, Bamboo, TFS Build entre outros.
(
<center>
  <img style="width: 45%; margin: 10px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/snap.png" />
  <img style="width: 30%; margin: 10px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/teamcity.jpg" />
<img style="width: 30%; margin: 10px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/tfs.jpg" />
  <img style="width: 45%; margin: 10px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/bamboo.png" />
</center>
)
Em linhas gerais, a construção da integração contínua consiste em organizar, parametrizar e definir os passos a serem realizados. Na imagem abaixo apresento um processo de integração contínua, e integração com ferramentas de qualidade de software e de deploy contínuo. De forma resumida, nesse processo específico para uma necessidade nossa temos os seguintes passos:

- Parametrização da integração
	- Define quais passos da integração deverão ser realizados. De forma geral é definido que sempre seja feita a análise de qualidade do código e o deploy. No entanto, manualemente esses processos podem ser desabilitados conforme a necessidade.
	
- Fontes
	- O próximo passo é baixar os códigos fontes. Os quais nesse processo devem ser compilados, testados e liberados em um determinado ambiente.
	
- Gatilhos
	- Dentro do Jenkins existem vários tipos de gatilhos que iniciam um processo. Nesse caso, foi utilizado um [CRON](https://en.wikipedia.org/wiki/Cron) para verificar a cada dois minutos se foi realizado algum commit no repositório de fontes.
	
- Ambiente de Build
	- É possível realizar algumas configurações de ambiente necessários para a realização do processo. Nesse caso, estou utilizando um plug-in de criação de versão, que posteriormente será utilizado para versionar as DLLs da compilação e o pacote de liberação.
	
- Build
	- Essa é a coração do Jenkins, onde configuramos o passo-a-passo que ele deve realizar. No casso desse processo são realizados os seguintes passos:
		- Remove a permissão de read-only dos arquivos. Pois o TFS baixa todos arquivos como read-only.
		- Plug-in onde altera a versão dos assemblies, para as DLLs assumirem a versão do Build.
		- Realiza o restore dos pacotes do Nuget.
		- Plug-in do MSBuild para realizar a compilação da solução.
		- Caso tenha sido definido que deverá analisar a qualidade do código:
			- Atualiza as bases de dados com os novos modelos de ER
			- Executa os testes funcionais das nossas aplicações WebAPI
			- Busca arquivos de cobertura de código e resultado dos testes executados
			- Envia a análise dos fontes e os resultados de testes para o nosso [SonarQube](http://www.sonarqube.org/)
		- Caso tenha sido definido que deverá realizar o deploy desse novo pacote
			- Gera pacotes de liberação das nossas aplicações WebAPI
			- Envia esses pacotes para nossa ferramenta de deploy
			- Jenkins sinaliza nossa ferramenta de deploy para gerar um novo Release e realizar o deploy em nosso ambiente de desenvolvimento
- Pós Build
	- Arquiva os resultados dos testes
	- Envia notificação caso o processo não tenha sido realizado com sucesso
			
<center>
  <img src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/ProjetoJenkins.png" />
</center>

<br>

<center>
  <img style="float: right; width: 250px; margin: 50px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/octopus-only-blue.png" />
</center>

Outra ferramenta que apoia nesse processo é o [Octopus Deploy](https://octopus.com/), responsável por gerenciar e controlar o processo de deploy dos pacotes em seus respectivos ambientes e configurações. Além de realizar o deploy em diversas máquinas de uma única vez quando estamos trabalhando com ambientes distribuídos. Sua configuração é baseada no conceito de servidor e agentes, onde o servidor é um portal onde o processo de deploy é configurado e os agentes são executáveis instalados nas máquinas que irão receber os deploys.

O Octopus não é uma ferramenta free, no entando possui um nível que permite até 5 projetos e até 10 servidores de deploy, o que no nosso caso comporta sem problemas. Como no caso do Jenkins, a utilização do Octopus foi uma opção feita por afinidade. Na minha opinião o Octopus, apesar de ser focada para deploys de aplicações .Net, possui uma interface amigável e de simples configuração e manutenção. Existem outra ferramentas de deploy no mercado como GO, Nexus entro outros.

Em linhas gerais, o Octopus permite que sejam criados ambientes e neles sejam associados os agentes conforme suas características, estágios de deploy e aplicações que cada uma deve receber. Conforme apresentado na imagem abaixo.

<center>
  <img style="margin: 20px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/Ambientes.png" />
</center>

Tendo os ambientes definidos, temos que configurar os projetos que iremos fazer deploy. Essa etapa é muito semelhante a construção do processo de integração contínua do Jenkins aonde definimos o que será liberado, onde, quais configurações, notificações, etapas de aprovações, execução de scritps, atualização de bases e entre outros. Também podemos definir o ciclo de vida do projeto, determinando quais ambientes o pacote deve passar até chegar ao final. Como apresentado na imagem abaixo.

<center>
  <img style="margin: 20px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/Processo.png" />
</center>

Tendo o projeto criado, há uma etapa onde é necessário realizar o upload do pacote via portal ou via publicação Nuget. Com isso, podemos criador a Release que é o conjunto de processos de deploy, variáveis de configuração e arquivos a serem liberados. É o Release que será liberado e promovido em cima do ciclo de vida do projeto. Conforme imagem abaixo.

<center>
  <img style="margin: 20px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/pacote.png" />
</center>

Com ambiente, projeto e pacotes construído, basicamente o que nos resta é solicitar o deploy dele em algum ambiente. E por consequência promovê-los dentro do seu ciclo de vida, como apresentado na imagem abaixo.

<center>
  <img style="margin: 20px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/promote.png" />
</center>

Com isso, agora é só acompanhar e solicitar atualizações. Sentar no banco e tomar chimarrão!
<center>
  <img style="margin: 20px" src="{{ site.baseurl }}/content/2016-04-13-integracao-continua-e-deploy-continuo-tempo-e-dinheiro/Deploy.png" />
</center>

Existem diversas formas de fazer, integrar e automatizar seu processo de integração. Seguindo a prática de [Kaizen](https://pt.wiktionary.org/wiki/kaizen), o foco não deve ser em automatizar todos seus processos de uma única vez, mas sim procurar automatizando, integrando seus pequenos processos e que podem dar um maior ganho de produtividade no seu dia-a-dia. Já dizia minha mãe, de grão em grão a galinha enche o papo! Tanto para o tempo ecônimizado, como no ganho de produtividade obtido. Eu particularmente tenho o conceito que se eu for fazer uma atividade mais de uma vez, é por que provavelmente eu vá faze-la diversas outras vezes. Então por que não automaizar?
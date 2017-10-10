---
layout: post
date: 2016-07-06T19:12:21-03:00
title: "Evolução de banco de dados na integração contínua"
author: gustavojotz
abstract: >
    Incluir a atualização do banco de dados na integração contínua é possível e muito mais simples do que você imagina.
---

Atualmente ter um ambiente de integração continua é um requisito fundamental em qualquer projeto de software. Em equipes que adotam a metodologia ágil no seu desenvolvimento, é praticamente impossível viver sem. Se não for possível implantar em produção de forma automatizada, ao menos a compilação, o empacotamento, a execução de testes unitários/integração e o deploy deveriam ser feitos nos ambientes intermediários.

A maioria dos sistemas necessita persistir as informações que são produzidas em algum local, seja num arquivo, num banco de dados, num serviço de repositório na nuvem ou outro local que mantenha o estado da informação e permita recuperá-la.  A grande maioria dos sistemas utiliza um banco de dados relacional para esse fim, seja ele um Oracle Database, Microsoft SQL Server, PostgreSQL ou MySQL.

Normalmente as pessoas não incluem na integração continua as alterações no banco de dados, as vezes por medo, outras pela utilização de ferramentas de modelagem de banco que não dão suporte, ou ainda, por não ter permissão de execução, necessitando que isso seja feito por um DBA.
<center style="margin: 20px">
  <img style="margin-top: 20px" src="{{ site.baseurl }}/content/2016-07-06-evolucao-de-banco-de-dados-na-integracao-continua/datical_manual.png" />
  <small>Modelo tradicional de integração continua sem BD no processo</small>
</center>

O modelo tradicional de evolução do banco de dados requer que os arquivos com as instruções sejam executados de forma manual por um DBA em cada um dos ambientes. Quando este não tem exclusividade no projeto, ele pode se tornar um gargalo. Acredito que todos já ouviram a frase: <i>(Nesse momento não posso, estou vendo um problema em produção”</i>, sendo que é necessário apenas atualizar a base de teste integrado.

A Datical em um [informe comercial](http://www.datical.com/wp-content/uploads/White-Paper-The-ROI-of-Automating-Database-Deployments.pdf), diz que 70% do tempo dos DBAs em uma grande corporação é gasto para aplicar instruções de atualização de banco decorrentes da evolução das aplicações. Desta forma, removendo ou simplificando esta tarefa há um ganho considerável de tempo e diminuição do custo, fazendo com que o DBA foque realmente naquilo em que ele é mais requisitado: manter o banco saudável e performático.

Neste post vou comentar duas soluções OpenSource, o [Flyway](https://flywaydb.org) e o [Liquibase](http://www.liquibase.org) que podem ser utilizadas para integrar de forma simples e rápida a evolução do banco de dados juntamente com seu processo de integração continua. Pode parecer apenas um detalhe, mas leia algumas perguntas que separei abaixo. Se alguma das respostas for negativa ou não souber responder, é bem provável que seu processo seja extremamente lento, de custo elevado e sua base de dados está correndo muito mais riscos do que deveria.

1. Consigo configurar de forma rápida uma nova instancia de banco no ambiente de desenvolvimento ou homologação?
2. O estado do banco de produção é compatível com qual versão da aplicação?
3. Quando foi efetuada a última atualização de banco? Quem foi o autor?
4. Posso atualizar rapidamente para a última versão?
5. Temos um bug na aplicação, precisamos voltar a versão, preciso fazer rollback, é rápido?


## Flyway

É uma API OpenSource escrita em Java mantido pela empresa alemã [Boxfuse](https://boxfuse.com/). Fornece suporte a praticamente todos os bancos de dados que possuem um driver JDBC e é extremamente simples de utilizar. Pode ser utilizada por linha de comando, por algum gerenciar de projetos estilo Maven/Gradle ou integrada a aplicação, neste caso Java, como o Spring Boot, Grails, entre outros e Android.

Sua utilização se baseia em arquivos de texto SQL prefixados com a versão. Através de uma tabela de controle a ferramenta verifica se cada arquivo foi aplicado, caso contrário, aplica-o. 

A ilustração abaixo mostra um exemplo da estruturação de pastas e arquivos: 
<center>
  <img style="margin-bottom: 20px" src="https://flywaydb.org/assets/balsamiq/SqlMigrationBaseDir.png" />
</center>

Todos os arquivos de texto SQL passam a ser versionados juntamente com os códigos fontes da aplicação, ou em algum repositório especifico para o bando dependendo da complexidade do ecossistema. O importante é mantê-los íntegros, jamais remover ou modificar o seu conteúdo depois de aplicado, pois a partir deles o banco pode ser atualizado a partir de qualquer versão.

Se utilizado por linha de comando, basta digitar a instrução:

```
$ flyway -configFile=conf/tst.conf migrate
```

## Liquibase
É uma API OpenSource escrita em Java mantida pela empresa [Datical](http://www.datical.com/liquibase). Tem suporte a maioria dos bancos de dados relacionais e ainda suporte estendido para alguns.

Diferente do Flyway, o Liquibase suporta diferentes formatos de arquivos de instrução. Estes são chamados de changelog file. Cada instrução declarada no arquivo é chamada de changeset. É possível criar um changelog no formato XML, YAML, JSON e também SQL.

Com o Liquibase, é possível trabalhar com branchs facilmente. Devido a sua estrutura flexível, pode-se adotar um modelo que facilite o merge. Isso permite que múltiplos desenvolvedores escrevam suas instruções sem grande preocupação de como juntá-las no final.

Uma grande vantagem do Liquibase é o suporte a rollback, lógica por contexto e pré-validações. Além disso, ainda tem uma funcionalidade para gerar um diff entre dois bancos, mesmo elas sendo de tipos diferentes (Oracle e SQL Server por exemplo).

A imagem a seguir ilustra uma maneira de estruturar um versionamento de banco de dados com o Liquibase.
<center>
  <img style="margin-bottom: 20px" src="{{ site.baseurl }}/content/2016-07-06-evolucao-de-banco-de-dados-na-integracao-continua/liquibase_model_app.png" />
</center>

Para atualizar o banco de dados de teste integrado, por exemplo, basta digitar:

```
$ liquibase --changeLogFile=changelog\changelog-master.xml --defaultsFile=config\tst.properies update
```

A Datical possui uma ferramenta de integração continua exclusiva para o Liquibase com diversas funcionalidades para controle, integração e automação.  



## Considerações Finais
Automatizar o processo de evolução do banco de dados é algo muito simples e já traz benefícios no primeiro momento em que for utilizado. Porém é aconselhável uma fase de experimentação e estudo da ferramenta. Sua utilização adequada requer disciplina e organização, um changeset do Liquibase, por exemplo, uma vez aplicado não poderá ser modificado, se o for, a ferramenta não continua a execução dos próximos até que seja revertido.

A utilização do rollback também requer cuidados, assim como na evolução, excluir tabelas ou colunas são procedimentos que não possuem procedimento de recuperação sem a utilização de algum backup.

Diferentemente da aplicação que em sua grande maioria não tem estado, ou seja, não tem informações dinâmicas, recuperar a versão anterior é simplesmente remover a atual e reimplantar a anterior. Mas se tratando de banco de dados esse procedimento não é possível, isso explica o fato da maioria das integrações continuas não incluir a atualização do banco no processo e deixar essa etapa como um passo manual.

IC para banco de dados é algo possível e altamente recomendado para qualquer tamanho de aplicação. 

Já utiliza uma ferramenta de automação de banco de dados? Compartilhe com nós a experiência.
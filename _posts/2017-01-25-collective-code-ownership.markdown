---
layout: post
date: 2017-01-25T09:53:21-03:00
title: "Collective Code Ownership - O código é de todos"
author: eskeff
abstract: >
  Collective Code Ownership
---

O que buscamos com a disseminação do conhecimento do código no projeto é justamente evitar situações em que somente um indivíduo é responsável ou “dono” de algum módulo ou tela do sistema. Imagina se esse projeto já está em produção e precisamos fazer um hotfix urgente para o cliente. Vamos dizer que no momento não podemos solucionar por que somente o Fulano sabe como?
Se todos da equipe se sentem confortáveis com o que foi construído facilita e muito a passagem de conhecimento para novos colegas, bem como agilidade para manutenções no código.

### Definição

Basicamente consiste em dividir a responsabilidade com todos do time, ou seja, ninguém é dono de alguma classe, método, tela  ou módulo em específico, todos são responsáveis pela manutenção e em garantir a qualidade do código do projeto.

### Algumas vantagens
  - Ter o conhecimento difundido de forma homogênea pela equipe, para isso o time deve constantemente trocar informações e assim aumenta o nível técnico dos demais;
  
  - Código acaba ficando muito mais legível e de fácil entendimento para todos, já que se deve aplicar diversas técnicas para garantir as boas práticas;
  
  - Time perde o receio de realizar manutenções e adição de novas funcionalidades em qualquer parte do sistema, principalmente a médio e longo prazo.
 
### Algumas desvantagens

- Se as cerimonias e uso de ferramentas para auxiliar o processo forem subutilizados, pode ocorrer o efeito contrario do esperado, como por exemplo:         
    - Atraso nas entregas
    - Um aumento de bugs devido a refactors
    - Pensar que "outra pessoa" irá fazer e deixar a tarefa no limbo 

- Dependendo dos envolvidos pode acabar causando conflito devido ideias diferentes e um certo "orgulho", e deve-se sempre ter o cuidado para contornar esse tipo de situação.
  


### Mas como aplicar essa prática no seu projeto?

Claro que a realidade dos projetos muitas vezes nos impede de atingir o mundo ideal devido custos e tempo do projeto, mas se tivermos as ferramentas e processos para nos auxiliar, o objetivo pode ser atingido.de forma mais eficiente, segue alguns deles:

 - Utilizar [Code Review](http://cwisoftware.github.io/drops/code-review) é uma das melhores maneiras de espalhar o conhecimento para a equipe.
 - Se possivel praticar o Pair Programming.
 - Deixar o código legível e de fácil manutenção como também demonstrado nos Drops [Code Smells](http://cwisoftware.github.io/drops/codesmells-dry-kiss)  e  [Design Patterns](http://cwisoftware.github.io/drops/design-patterns) 
 - Ter uma padrão forte, consistente e bem documentado ajuda o desenvolvedor que precisar alterar outro modulo de seu projeto não se sinta perdido. 
 - E seguir esse padrão, se não concordar, discuta com o time, mas não quebre o padrão sozinho e de forma silenciosa.
 - Não ter medo de refatorar o código caso necessário.
 - Testes unitários para toda funcionalidade e manutenção do código, para garantir a integridade do projeto e deixar confortável para quem for aplicar a mudança.
 - Testes de aceitação automatizados 
 - Fazer uso de [Integração Contínua](http://cwisoftware.github.io/drops/maturidade-integracao-continua) que executa esses testes a cada nova mudança.

Essas são apenas alguns processos e ferramentas que auxiliam e muito o dia a dia, para ser possível aplicar espalhar o conhecimentos para todos.

### Conclusão

Sempre ter em mente que todos devem trabalhar para o bem do projeto, e não somente para se livrar de uma tarefa.
E isso significa se preocupar com a qualidade, se envolver com o time, assumir responsabilidades, ter opinião e defender suas ideias. 

### Referências

 - [Collective Code Ownership](http://wiki.c2.com/?CollectiveCodeOwnership)
 - [Collective Ownership](http://www.extremeprogramming.org/rules/collective.html)
 - [Are there weaknesses with Collective Code Ownership?](https://www.infoq.com/news/2008/05/weaknesses_collective_code) 
 - [CodeOwnership](https://www.martinfowler.com/bliki/CodeOwnership.html)
 - [Code Ownership – Who Should Own the Code?](http://swreflections.blogspot.com.br/2013/04/code-ownership-who-should-own-code.html)
 - [Revisiting XP: be a thoughtful programmer by exercising more collective ownership](https://www.infoq.com/articles/revisit-p-collective)





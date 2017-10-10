---
layout: post
date: 2017-01-18T00:08:00-02:00
title: "Code Review - Uma introdução ao processo"
author: sergiomichels
abstract: >
  Introdução as técnicas para Code Review, com seus pontos positivos e negativos, elencando benefícios no uso do processo. 
---

Imaginem o seguinte cenário: você está iniciando em um projeto que já possui uma certa gama de funcionalidades, e para se ambientar, lhe pediram para atuar na correção de alguns bugs. Da lista, após ler as descrições, seleciona um que parece tranquilo de ser resolvido.

Após, segue para o momento mais empolgante no dia do desenvolvedor, pegar o seu cafézinho e trabalhar no código fonte. Entusiasmado, encontra a classe responsável pelo processo.

Resolve dar uma olhada superficial primeiro, só usando o scroll do mouse, mas putz! O fonte possui mais de 3000 linhas, os mais variados métodos públicos e privados, constantes com nomes estranhos (tais como "valor1" e acreditem, já vi algumas destas), e você se sente perdido.

Pensa então em pedir ajuda ao colega, pra que ele te dê uma explicação, sem que seja mais macro. Eis que recebe a seguinte resposta

"Bah! Só o fulano que entende o que se passa nessa classe, se tu mudar ela pode quebrar vários lugares, e ele saiu de férias..."

Se identificou? Pois é, e pensar que adotar o Code Review poderia ajudar a eliminar esses problemas :)

### O Code Review

Code review (também conhecido Peer Code Review) é uma fase no desenvolvimento de software onde se realiza uma verificação em parte do código fonte, antes de disponibilizar este fonte para testes ou colocá-lo em produção, com finalidade de encontrar problemas ou melhorias. Já vimos aqui no Drops o que são [Code Smells](http://cwisoftware.github.io/drops/codesmells-dry-kiss) e o Code Review pode nos ajudar a identificá-los antes que eles se tornem uma dor de cabeça maior.

A menos que o projeto tenha apenas um desenvolvedor, esta verificação é feita por um colega, que pode trazer uma visão diferente em relação a solução.  

### Tipos de Code Review

Existem diferentes processos que podem ser aplicados e cabe uma avaliação de qual seria mais efetivo dentro do contexto de cada time/projeto. Citando alguns, temos

#### Over The Shoulder 

Neste método, assim que o desenvolvedor termina a sua implementação, convida alguém para sentar ao seu lado e explicar o que foi feito. Uma outra abordagem é deixar que o avaliador navegue pelo fonte, respondendo a possíveis dúvidas.

Por ser simples e informal, pode ser a porta de entrada para times que ainda não realizam o processo, e que não dispõem de muitas horas para investir no review. Como pontos de atenção eu destacaria

 - A quantidade de código a ser revisado depende do momento em que o desenvolvedor decide solicitar a avaliação.
 - Uma execução rápida pode deixar passar alguns detalhes que poderiam ser encontrados em uma avaliação mas minuciosa.

Por si só, esta técnica não coloca bloqueios para que o código seja integrado, sendo necessário alguma ação complementar caso isso seja necessário.    

#### Pair Programming 

Na minha visão um dos mais interessantes, pode ser utilizado para repassar conhecimento, ou quando a complexidade da funcionalidade é alta, por exemplo. 

Nele, dois programadores desenvolvem juntos a tarefa, utilizando apenas um computador. Um desenvolvedor, chamado de *driver*, é responsável pela escrita do código enquanto o outro (o *observer*) fica responsável por avaliar o que está sendo escrito.

Esta técnica depende da personalidade das pessoas envolvidas que precisariam estar confortáveis sendo avaliadas enquanto escrevem o fonte, por isso o time pode ter mais dificuldade na sua adesão. Além disso ela pode ser mais difícil de justificar, pois são dois desenvolvedores atuando em uma única tarefa.

#### Utilizando Ferramentas 

Solução para times distribuídos ou para os casos que demandam um processo mais rígido, existem [diversos softwares](http://devzum.com/2015/04/best-code-review-tools/) com a finalidade de auxiliar no Code Review. Dentre as funcionalidades, podemos elencar

 - Permite que o fonte seja adicionado na branch principal somente após o review
 - Permite que o review seja colaborativo, através de comentários e propostas de soluções
 - Pode conter regras, como por exemplo a obrigatoriedade de duas ou mais avaliações 
 - Pode conter mecanismos para coletar métricas, ficando mais visível os benefícios da sua execução

 Um destes softwares (utilizado na CWI) é o GitLab, que através de seu processo de Merge Request permite que seja adicionado o fluxo de Code Review, antes que aquele fragmento de código seja adicionado a branch principal. 
 [Neste artigo](https://yalantis.com/blog/code-review-via-gitlab-merge-requests-code-review-must/) pode-se verificar quais são os passos necessários para implementar esse processo dentro da ferramenta.

### Alguns Benefícios 

Dentre os benefícios no uso do Code Review, podemos citar

- Desenvolvedores com mais experiência podem auxiliar iniciantes (clean code, performance, design patterns, segurança), onde a curva de aprendizagem se torna menor.
- A responsabilidade do software produzido passa a ser do time, evitando a ideia de único dono de determinada funcionalidade.
- Bugs são identificados e corrigidos antes de chegarem em produção.
- Possíveis características específicas que não foram documentadas (sim, nem sempre peculiaridades estão documentadas) podem ser explicadas.
- Apesar de ser um investimento, pode economizar tempo que seria gasto detectando e corrigindo falhas.
- Melhoria na qualidade do código.

### Mais Sobre Code Review

- [Giving Better Code Reviews](https://medium.com/@mrjoelkemp/giving-better-code-reviews-16109e0fdd36#.b6diqgvfp)

- [Increase Defect Detection With Code Review Checklist](https://blog.fogcreek.com/increase-defect-detection-with-our-code-review-checklist-example/)

- [Java Code Review Checklist](https://dzone.com/articles/java-code-review-checklist)

- [PDF: Best Kept Secrets of Peer Code Review](https://smartbear.com/SmartBear/media/pdfs/best-kept-secrets-of-peer-code-review.pdf)

- [Effective Navigation in Pair Programming](https://www.thoughtworks.com/insights/blog/effective-navigation-in-pair-programming)

### Referências

- [What is Code Review?](https://smartbear.com/learn/code-review/what-is-code-review/)

- [Why Code Reviews Matter](https://www.atlassian.com/agile/code-reviews)

- [Code Review Via GitLab Merge Requests](https://yalantis.com/blog/code-review-via-gitlab-merge-requests-code-review-must/)

- [PDF: Best Kept Secrets of Peer Code Review](https://smartbear.com/SmartBear/media/pdfs/best-kept-secrets-of-peer-code-review.pdf)

- [Four Ways to a Practical Code Review](http://www.methodsandtools.com/archive/archive.php?id=66)

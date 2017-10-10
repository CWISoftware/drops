---
layout: post
date: 2017-03-27T09:44:43-03:00
title: "Introdução ao Vuejs"
author: calvingerling
abstract: "Como é e quais as vantagens?"
---

Hoje em dia, ao falar sobre desenvolvimento web, é inevitável mencionar as inúmeras libraries/frameworks JavaScript focadas em __MV*__, que facilitam o desenvolvimento. Criada em 2014 por Evan You, o [Vue](https://vuejs.org) é mais uma delas, que se destaca por sua flexibilidade e escalabilidade ao ser "um conjunto de ferramentas que trabalham muito bem juntas", ou seja, enquanto seu core foca somente na "exibição de dados" na tela, o conjunto pode ser complementado com "plugins" para novos comportamentos (e.g. requisições _http_ e rotas). Essa é uma abordagem diferente do Angular, que é um "monolito" com tudo e muito mais desde o primeiro uso.

Apesar de não superar o Angular ou o React em popularidade, o uso da mesma vem crescendo entre os desenvolvedores. É possível ter uma noção disso a partir das stars no repositório oficial do mesmo. Abaixo, o comparativo do número de stars nos repositórios dos principais "concorrentes" desde as suas criações:

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-03-20-vue-introducao/github-stars.png" />
  <p>Imagem 1.1 - Angular: 55114, React: 62282 e Vue: 47282</p>
</center>

Mas não é só com stars que um framework se mantém atrativo. Para isso, é preciso uma boa documentação que sirva tanto para aventureiros que estão recém começando quanto para os já familiarizados, uma comunidade engajada com o uso da ferramenta e showcases, como a página [News Feed](https://newsfeed.fb.com/) do Facebook, que por não possuir a necessidade de recursos avançados, usa somente o core da library para formar a mesma, a [migração do GitLab](https://about.gitlab.com/2016/10/20/why-we-chose-vue/) para Vue ou do Laravel que a partir da [versão 5.3](https://laravel-news.com/laravel-5-3-is-now-released) já vem com o Vue embutido.

## Single-file component
<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-03-20-vue-introducao/hello-world-component.png" />
</center>
O conceito de single-file component é centralizar em um único arquivo tudo aquilo que compõe o componente - estrutura, comportamento e visual. Esse tipo de abordagem traz certas vantagens, como: 

- Melhor visualização de cada parte do componente;
- Possibilitar o uso de _supersets_(Jade, Sass, TypeScript, ...) de forma independente dentro do componente;
- Possibilitar o reuso de partes do sistema ao centralizar os componentes.

Seu uso só é possível caso seja usado junto com ferramentas como Webpack, Browserify ou Rollup.

## Virtual DOM
Durante o ciclo de vida de uma página - principalmente no caso de SPAs - existe a necessidade de modificar a DOM diversas vezes, processo cujo custo é alto. A Virtual DOM entra como intermediário nesse processo por ser uma representação mais leve da DOM que possui os mesmos elementos que a mesma. Seu funcionamento consiste em receber as alterações no lugar da DOM e, quando essas alterações estiverem "completas" serão repassadas para a DOM. A implementação do Vue consegue economizar ainda mais alterações pois, durante a compilação dos templates para a Virtual DOM, o mesmo rastreia quais dependências mudam e atualiza somente estas.

Esse mecanismo só está presente a partir da versão 2.0. Na versão anterior o processo acontece de forma distinta, onde cada mudança em um componente não é repassada imediatamente para o DOM e é feita de modo independente. Assim, não é necessária a revalidação dos demais componentes que não são afetados pela mudança, exceto se a mesma afetar diretamente as dependências do componente em que ela ocorreu.

Existe uma série de comparativos, que levam em conta tanto a performance quanto a usabilidade, entre os seguintes frameworks: Angular 1, Angular 2, React, Ember, Knockout, Polymer e Riot. Disponível [aqui](https://vuejs.org/v2/guide/comparison.html). 

## TL;DR;
Vue é uma library leve e flexível que consegue se adaptar às necessidades de um projeto, desde páginas simples até _web applications_, através de plugins que podem ser "acoplados" ao seu _core_ e dar novas funcionalidades a ele. Permite a criação de componentes de forma centralizada e organizada onde todos os aspectos - estrutura, comportamento e aparência - são mantidos no mesmo arquivo. Além de ser leve "fisicamente" também o é no _runtime_, ao utilizar mecanismos como _Virtual DOM_ e o rastreamento das alterações em cada componente.

Referências:

* [Gráfico de Stars do GitHub](http://www.timqian.com/star-history/#angular/angular.jsfacebook/react&vuejs/vue)

---
layout: post
date: 2017-10-06T16:44:43-03:00
title: "Vue.js: simples e escalável"
author: victorcomette
abstract: "Uma visão sobre a aplicação do framework em projetos."
---

**Vue** é um framework para JavaScript revolucionário que com certeza irá resolver TODOS os seus problemas!!! Não convence não é mesmo?! A verdade é que, em meio a um oceano de frameworks, determinar o "melhor" para uma finalidade é quase sempre um erro. Cada biblioteca traz suas próprias vantagens e desvantagem, cabendo à equipe pesquisar e definir qual utilizar em seus projetos. Nesse post, abordarei um pouco sobre minha visão ~~tendenciosa~~ dos pontos em que o Vue se destaca, os benefícios que vejo na sua aplicação em projetos e as diferenças em relação aos seus principais concorrentes: o *Angular* e *React*.

   

### **Menos é mais!**

A modularização do Vue é uma das primeiras vantagens que percebemos. Em contraste com o Angular, que disponibiliza no modelo "tudo ou nada", temos um cenário em que as soluções são oferecidas por demanda. A biblioteca principal pode ser utilizada simplesmente colocando a tag abaixo no seu `index.html`

```html
<script src="https://unpkg.com/vue"></script>
```

Apenas com essa referência de um `CDN` você já consegue escrever código Vue. Sem configurações complexas, sem baixar dezenas de dependências; basta referenciar o script e distribuir a versão minificada em produção.
   

> Precisa de mais funcionalidades? O ecossistema Vue possui diversas bibliotecas adicionais, que funcionam de forma independente e flexível, entregando apenas aquilo que interessa e tem utilidade.

Isso não significa que seja um framework básico, ou incompleto. Pelo contrário, as bibliotecas adicionais são muito robustas e oferencem soluções completas como `vue-router`, `vue-loader` e `vue-server-renderer`, que aprenderam com seus concorrentes, incorporando o que já funcionava e melhorando algumas funções, como o caso do `Vuex`/`Redux` (*Flux*). Não está convencido? Não tem problema, o Vue é flexível e compatível com outras bibliotecas como, por exemplo *Page js* para rotas.

Mas nem tudo são flores. Por ser um framework novo (2014), ainda falta um bom caminho até que sua utilização esteja difundida e consolidada, com abundância de artigos e coisas "prontas" para serem aproveitadas em projetos. Em contra-partida, o framework possui ferramentas muito simples para criação de *plugins*, *mixins* e *diretivas* o que incentiva a comunidade.
   

> Outra ferramenta muito útil em vários casos é o `vue-cli`, o projeto de templates oficial do ecossistema, que traz várias possibilidades de customização e configurações completas, com `webpack`/`browserify`, testes unitários, lint e etc.

   

### **Aplicabilidade**

Acredito que é neste quesito que o framework se destaca. Quando trabalhamos em cenários com projetos de curto e de médio prazo, o Vue é muito assertivo em dar ao desenvolvedor liberdade para trabalhar. Diferente do *Angular 2* que força a utilização do *Typescript* (ainda que isso possa ser evitado), com Vue temos total liberdade para programar com *Typescript, Coffescript, ES2015,* inclusive utilizando compiladores como *Babel* ou *Buble*.

Essa liberdade se estende para o `template` que pode ser escrito com *HTML* puro, através de compiladores como *~~Jade~~ Pug*, ou ainda por meio de `render functions` (`JSX`) como é feito no *React*. Em termos de `style` a versatilidade se mantém, podendo se aplicar facilmente estilos com css puro, less, sass e etc.

Com toda essa flexibilidade é mais simples para desenvolvedores que trabalham com *Angular* ou *React* migrarem para o Vue, pois a curva de adaptação torna-se pequena. Qualquer desenvolvedor com conhecimento em JS, HTML e CSS puros consegue ser produtivo com Vue. Isso facilita muito na hora de montar equipes, incluir novos desenvolvedores ou repor posições, pois o conhecimento "específico" necessário sintetiza-se apenas nas funções da API, que ainda por cima é de fácil aprendizado.

   

### **No fim das contas...**

Temos no Vue um framework versátil, flexível e inclusivo, com uma já grande aceitação pelo desenvolvedores e que ganha cada vez mais espaço no mercado, como já mostrado neste [outro Drop](http://cwisoftware.github.io/drops/vue-introducao) sobre Vue. Ainda falta bastante para a biblioteca ser tão conhecida como o Angular, mas as melhorias que ela oferece tem alavancado sua aplicação e popularidade rapidamente. Em paralelo, é interessante ver uma crescente [comunidade brasileira](http://www.vuejs-brasil.com.br/) dedicada ao Vue produzindo conhecimento sobre o framework e consolidando sua fatia do mercado brasileiro.
   

> Em breve virão novos posts mais técnicos e, enquanto isso, espero ter despertado sua curiosidade sobre sobre essa belezinha!
   

#### **Referências**

* https://vuejs.org/
* http://www.vuejs-brasil.com.br/
* https://www.valuecoders.com/blog/technology-and-apps/vue-js-comparison-angular-react/
* https://vuejs.org/v2/guide/comparison.html

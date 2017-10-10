---
layout: post
date: 2017-04-18T13:00:00-03:00
title: "Automação de testes end-to-end para aplicações AngularJS"
author: josebarbosa
abstract: >
  Estrutura base para novos projetos 
---

Com o intuito de agilizar o *start* de projetos de automação, iniciamos uma série de posts sobre estruturas em diversas linguagens que podem auxiliar neste processo.
Iniciamos com o projeto [cwi-protractor-core](http://git.cwi.com.br/Fabrica-de-Testes/cwi-protractor-core), construído para realizar testes em aplicações desenvolvidas com AngularJS.

#### Primeiros passos
Este projeto é feito com [protractor](http://www.protractortest.org/), e para utilizá-lo será necessário instalar o [nodejs](https://nodejs.org/en/).  
Depois de instalado você utilizará linhas de comando para gerenciar o projeto e usar o nodejs. Para isso você tem algumas opções como [gitbash](https://git-for-windows.github.io/), [powershell](https://github.com/PowerShell/PowerShell),  o prompt do Windows ou Terminal do Linux. Minha preferência é o powershell, já que é integrado ao [Visual Studio Code](https://code.visualstudio.com/) (IDE que utilizo para este tipo de projeto).  
Depois de instalar as dependências, inicie seu programa de linha de comando escolhido e execute o comando `node -v`. Esse comando deverá mostrar a versão do nodejs instalada, caso apresente erro verifique se o nodejs foi instalado corretamente.

#### IDE
Como mencionei acima, utilizo a IDE [VSCode](https://code.visualstudio.com/) com as seguintes extensões:
- vscode-icons
- TSLint
- Path Autocomplete
- Git: Common Commands Suite
- JavaScript (ES6) code snippets

Mas pode ser utilizada a IDE de sua preferência.

#### Clonando o projeto
Para ter o projeto é necessário clonar ele usando o comando `git clone git@git.cwi.com.br:Fabrica-de-Testes/cwi-protractor-core.git`  
  
Pronto, o projeto agora está clonado.

#### Iniciando
Após clonar o projeto do [gitlab](http://git.cwi.com.br/Fabrica-de-Testes/cwi-protractor-core), use o comando `npm i` para instalar as dependências do projeto. Depois `npm run test` para executá-lo. Nenhuma outra configuração deve ser necessária para iniciar.

#### Mapeando os objetos da tela com a utilização de PageObjects
Para que os testes sejam de fácil interpretação, utilizamos o padrão [PageObjects](https://github.com/SeleniumHQ/selenium/wiki/PageObjects), que conforme a própria [documentação da ferramenta](http://www.protractortest.org/#/page-objects), auxilia no reaproveitamento de código e o deixa mais *clean*.

Os arquivos com o mapeamento das telas devem ser estruturados a partir da pasta `src/pageObjects/`.

#### Testes
Os arquivos com os testes devem ser estruturados a partir da pasta `test/`. Serão executados os arquivos que terminem com `.spec.ts` ou `.feature.ts`. É possível estruturar os testes nos formatos abaixo:

- Cenários descritos em um único arquivo BDD

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-04-18-automacao-de-testes-e2e-angularjs/1.png" />
</center>

- Cenários descritos em um arquivo com a feature BDD e outro com a implementação

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-04-18-automacao-de-testes-e2e-angularjs/2.png" />
</center>

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-04-18-automacao-de-testes-e2e-angularjs/3.png" />
</center>

#### Arquivo de Configuração

Para definir o navegador em que a aplicação será testada, bem como outros parâmetros de inicialização, é necessário criar um [arquivo de configuração](http://www.protractortest.org/#/api-overview#config-files), conforme abaixo:

    // exemplo de arquivo de configuração
    exports.config = {
      // url em que está rodando o selenium server.
      seleniumAddress: 'http://localhost:4444/wd/hub',
    
      // Capacidades de execução do webdriver (como qual navegador deve rodar o teste, descrita abaixo).
      capabilities: {
        'browserName': 'chrome'
      },
    
      // caminho para o arquivo e/ou padrão de busca para arquivos de teste
      specs: ['teste-exemplo-spec.js', 'test/**/*.spec.js'],

      }
    };

#### Relatório

É possível adicionar um relatório através da chamada abaixo:

    onPrepare: () => {
	    jasmine.getEnv().addReporter(new JasmineReporter(jasmine, {}));
    }

O relatório gerado estará na pasta `./reports/` e será semalhante ao exemplo abaixo:

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-04-18-automacao-de-testes-e2e-angularjs/4.png" />
</center>

Este projeto foi desenvolvido para toda a comunidade interna da CWI. Então baixe, utilize e contribua para sua evolução.

URL do projeto no gitlab: http://git.cwi.com.br/Fabrica-de-Testes/cwi-protractor-core
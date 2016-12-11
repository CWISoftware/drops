---
layout: post
date: 2016-11-25T12:00:00-00:00
title: "Design Patterns"
author: giovanidecusati
abstract: Padrões de código com *Design Patterns*
---
Definir um bom conjunto de classes, interfaces e métodos são um bom começo para quem quer produzir código de boa qualidade. Dificilmente encontraremos um problema que não tenha solução pronta. Basicamente, os problemas mais comuns de design são resolvidos por meio de soluções denominadas "Design Patterns”. Os patterns como são chamados permitem reduzir a complexidade do código proporcionando abstrações já prontas para o uso, fazendo com que você não precise comentar cada linha do seu código para que outros programadores entendam a sua implementação. Ao padronizar estas soluções erros comuns de implementação são reduzidos, uma vez que estes problemas vêm sido resolvidos inúmeras vezes por décadas. Além do mais, os programadores que estão familiarizados com os design patterns são capazes de facilmente reconhecer o problema e apresentar uma solução legível e que será interpretada por outros programadores. Assim, elevando o nível do seu código pondo em foco as decisões de design tomadas ao invés de tentar "reinventar” soluções inadequadas. Os Design Patterns estão categorizadas em três grupos: Creational, Structural e Behavioral.

## Creational Patterns

### Factory
"Fornecer uma interface para criação de famílias de objetos relacionados ou dependentes sem especificar suas classes concretas."

### Simple Factory: 
Encapsula o processo de criação de uma instância de um objeto.
- [Simple Factory](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Simple%20Factory)

### Factory Method: 
Define uma interface para criar um objeto, deixando a classe que implementa a interface decidir qual classe instanciar.
- [Factory Method](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Factory%20Method)

### Abstract Factory: 
Define uma interface pra criar familias de objetos sem a necessidade expor a concreta implementação. 
- [Abstract Factory](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Abstract%20Factory)

### Builder
"Separar a construção de um objeto complexo de sua representação de modo que o mesmo processo de construção possa criar diferentes representações."
- [Builder](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Builder%20Pattern)

### Prototype
"Especificar tipos de objetos a serem criados usando uma instância protótipo e criar novos objetos pela cópia desse protótipo."
Funciona criando cópias de um objeto já pronto (instanciado), muito útil para otimizar o processo de instanciar objetos complexos e demorados.  
- [Prototype](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Prototype%20Pattern)

### Singleton
"Garantir que uma classe tenha somente uma instância e fornece um ponto global de acesso para a mesma." 
- [Singleton](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/1.%20Creational%20Patterns/Singleton%20Pattern)

## Structural Patterns

### Builder
"Separar a construção de um objeto complexo de sua representação de modo que o mesmo processo de construção possa criar diferentes representações."
- [Bridge Patterns](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/2.%20Structural%20Patterns/Bridge%20Pattern)

### Proxy
"Fornecer um substituto ou marcador da localização de outro objeto para controlar o acesso a esse objeto."
- [Proxy Patterns](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/2.%20Structural%20Patterns/Proxy%20Pattern)

### Adapter
"Converter a interface de uma classe em outra interface, esperada pelo cliente. O Adapter permite que interfaces incompatíveis trabalhem em conjunto – o que, de outra forma, seria impossível."
- [Adapter](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/2.%20Structural%20Patterns/Adapter)

### Composite
"Compor objetos em estruturas de árvore para representar hierarquia partes-todo. Composite permite aos clientes tratarem de maneira uniforme objetos individuais e composições de objetos.”
- [Composite](http://www.codeproject.com/Articles/185797/Composite-Design-Pattern)

### Decorator
"Dinamicamente, agregar responsabilidades adicionais a objetos. Os Decorators fornecem uma alternativa flexível ao uso de subclasses para extensão de funcionalidades."
- [Decorator](http://www.dofactory.com/net/decorator-design-pattern)

### Facade
"Fornecer uma interface unificada para um conjunto de interfaces em um subsistema. Facade define uma interface de nível mais alto que torna o subsistema mais fácil de ser usado."
- [Facade](http://www.dofactory.com/net/facade-design-pattern)

## Behavioral Patterns

### Strategy
"Definir uma família de algoritmos, encapsular cada uma delas e torná-las intercambiáveis. Strategy permite que o algoritmo varie independentemente dos clientes que o utilizam."
- [Strategy](https://github.com/fabriciorissetto/design-patterns-examples/tree/master/3.%20Behavioral%20Patterns/Strategy%20Pattern)

### Command
"Encapsular uma solicitação como objeto, desta forma permitindo parametrizar cliente com diferentes solicitações, enfileirar ou fazer o registro de solicitações e suportar operações que podem ser desfeitas."
- [Command](http://www.dofactory.com/net/command-design-pattern)

### Observer
"Definir uma dependência um para muitos entre objetos, de maneira que quando um objeto muda de estado todos os seus dependentes são notificados e atualizados automaticamente."
- [Observer](http://www.dofactory.com/net/observer-design-pattern)

## Referências
 * GAMMA, Erich et al. Padrões de Projeto: Soluções reutilizáveis de software orientado a objetos.
 * .Net Design Patterns. Disponível em: <http://www.dofactory.com/net/design-patterns>
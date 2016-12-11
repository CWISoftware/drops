---
layout: post
date: 2016-12-08T13:00:00-03:00
title: "Clean Code, Code Smells, DRY e KISS. Em busca do código ideal."
author: guilhermepohlmann
abstract: >
  Entendendo alguns dos princípios para um código limpo e de fácil manutenção. 
---

Nós, como desenvolvedores e engenheiros de software, temos como missão resolver problemas através do nosso código. Mas somente resolver o problema não é suficiente, pois o nosso código pode acabar se tornando o problema. Como profissionais, devemos nos preocupar em manter um código de fácil leitura, compreensão e manutenção. Devemos pensar não só em nós, mas nas próximas pessoas que irão evoluir e dar manutenção nesse código. É aqui que entram os princípios de Clean Code, e é aqui que surgem os Code Smells. Você já olhou seu código de 6 meses, ou até de anos atrás? E você pensou, "Mas que diabos eu fiz aqui... “? Então provavelmente você já fez um Code Smell.

### O que são Code Smells ?

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-12-06-codesmells-dry-kiss\clean-code.jpg" />
</center>

Code Smells são aqueles pedaços de código que provavelmente podem ser escritos de uma maneira melhor. Outra maneira de encarar os Smells é como violações das boas práticas e da qualidade de código.

#### Eles são realmente ruims ?

Você está se questionando se terá problemas ou se seu código simplesmente vai ficar feio? Sim, você pode ter problemas. Os Smells normalmente estão ligados a problemas de Design mais profundos na aplicação, e podem também causar grandes impactos em performance.

#### E agora, o que eu faço ?

[“If it stinks, change it” (M. Fowler, Refactoring).](http://www.martinfowler.com/books/refactoring.html)
Não é regra que um Smell deve ser refatorado imediatamente, você pode julgar o impacto e tomar a melhor decisão, priorizando aqueles que realmente podem ser tornar um problema. Além do mais, se você já tem um bom Design, ou uma boa orientação a objeto, você já tem espaço para pensar em melhorias.

#### Quais os Smells mais comuns e seus impactos ?

#### **1. Código duplicado**

O mesmo código, ou muito similar, aparece várias vezes na aplicação.

**Problemas**

- Uma correção de um Bug em um ponto não vai ser propagada para todos os outros.
- Faz o código ser maior do que realmente precisa.

**Solução**

- Encapsule o código duplicado em um lugar comum onde todas as partes podem utilizá-lo.

#### **2. Métodos muito longos**

Um método que possui muitas linhas de código.

**Problemas**

- Quanto maior o método, mais difícil de entender, alterar e até de reutilizar.

**Solução**

- Você pode extrair trechos do método em métodos menores com nomes sugestivos e chama-los dentro do "não mais tão grande método".

#### **3. Nomes sugestivos**

Variáveis, classes ou métodos não possuem nomes de acordo com a sua função.

**Problemas**

- Se torna difícil para entender o propósito do código e dar manutenção no mesmo.

**Solução**

- Renomeie as variáveis para nomes sugestivos (essa foi fácil).

Veja o exemplo abaixo:

	public class SmellClass
	{
		public void SmellMethod()
		{
			int t; //O que isso faz? 
			int t; //Número de ticks em um minuto ( melhor ? )
			int ticksPerMinute; //E agora?
		}
	}

#### **4. Separação de responsabilidade**

Separação de responsabilidades é um conceito que pode ser aplicado em classes ou métodos.

**Problemas**

- Uma separação de responsabilidades ruim pode levar a vários problemas, dentre os principais podemos citar difícil legibilidade e manutenabilidade, 

**Solução**

- Pense com cuidado na hora de separar as responsabilidades de uma classe ou método, se possível, utilize os conceitos de apresentados sobre código duplicado, métodos muito longos e nomes sugestivos.

Veja o exemplo abaixo:

	public class Paciente
	{
		public string Cpf { get; set; }

        public string Cns { get; set; }

        public string Rg { get; set; }
	
		public bool ValidarDadosSmell()
		{
			if(!string.IsNullOrEmpty(Cpf))
			{
				//Valida se o Cpf é válido
			}
			
			if(!string.IsNullOrEmpty(Rg))
			{
				//Valida se o Rg é válido
			}
			
			if(!string.IsNullOrEmpty(Cns))
			{
				//Valid se o Cns é válido
			}
		}
		
		public bool ValidarDados()
		{
			return ValidarCpf() &&
				   ValidarRg() &&
				   ValidarCns();
		}
	}

#### **5. Complexidade ciclomática**

A Complexidade ciclomática é utilizada para medir a complexidade de um software ou pedaço de software. Diversas ferramentas de análise de código utilizam essas métricas. Essa complexidade é computada através do número diferentes de fluxo que o código possui.

**Problemas**

- Quanto maior a complexidade ciclomática de um método ou classe, maior o número de fluxos, ou seja, se torna difícil qualquer tipo de trabalho em cima desse código.
- O código com uma complexidade alta pode acabar levando a problemas de performance. 

**Solução**

- Utilize os conceitos apresentados anteriormente de separação de responsabilidades.
- Tenha cuidado com algumas práticas simples que podem levar a uma complexidade maior.

Veja o exemplo abaixo:

	public class SmellClass
	{
		//Esse código possui uma complexidade de 2. Todo cálculo de complexidade já inicia em 1.
		//E aumenta 1 para cada desvio de fluxo, nesse caso um if e um else.
		public bool ValidateSmell(SomeClass someClass)
		{
			if(someClass != null)
			{
				return true;
			}
			else
			{
				return false;
			}
		}
		
		//Esse código possui complexidade de 1.
		public bool Validate(SomeClass someClass)
		{
			return someClass != null;
		}
	}
	
#### Alguns princípios "anti smells"
	
#### 1. Orientação a objetos

Orientação a objetos mal explorada pode levar a problemas de design e de manutenabilidade.

		public enum EmployeeType
        {
            Comissioned,
            Hourly,
            Salaried
        }

        public class Employee
        {
            public EmployeeType Type { get; set; }

            public static Money CalculatePay(Employee e)
            {
                switch(e.Type)
                {
                    case EmployeeType.Comissioned:
                        return CalculateComissionedPay(e);
                    case EmployeeType.Hourly:
                        return CalculeteHourlyPay(e);
                    case EmployeeType.Salaried:
                        return CalculeteSalariedPay(e);
                }
            }

        }
		
O código acima ficaria muito melhor escrito com OO eficiente, utilizando Employee como classe abstrata e criando outras classes que herdam essa para implementar o método CalculetePay.
		
		public abstract class Employee
        {
            public abstract Money CalculatePay();
        }

        public class ComissionedEmployee : Employee
        {
            public override Money CalculatePay()
            {
                return new Money();
            }
        }
		
#### 2. Dont't repeat yourself (DRY)

DRY é um princípio que pode ser aplicado ao sistema inteiro, quanto a pequenas partes do seu código. Ele foi desenhado para reduzir a repetição de informações, de todos os tipos. Conforme você divide seu sistema em componentes e subcomponentes, você vai chegar em um nível onde a complexidade se reduz a uma responsabilidade. Essas responsabilidades podem ser implementadas em classes, métodos e algoritmos.

		static void ChooseSmell(int option)
        {
            switch (option)
            {
                case 1:
                    Console.Clear();
                    DoSomething("A");
					DoSomething("C");
					DoSomething("D");
                    break;

                case 2:
                    Console.Clear();
                    DoSomethingElse();
                    break;

                case 3:
                    Console.Clear();
                    DoAnotherThing();
                    break;

                case 4:
                    Console.Clear();
                    DoSomethingExtraordinary();
                    break;
            }
        }
		
		private readonly static List<string> Things = new List<string>() { "A", "B", "C" };
		
		static void Choose(int option)
        {
			Console.Clear();
		
            switch (option)
            {
                case 1:
                    Things.ForEach(thing => DoSomething(thing));
                    break;

                case 2:
                    DoSomethingElse();
                    break;

                case 3:
                    DoAnotherThing();
                    break;

                case 4:
                    DoSomethingExtraordinary();
                    break;
            }
        }
		
#### 3. Keep it Simple Stupid (KISS)

"A explicação mais simples tende a ser a certa." As vezes podemos tentar complicar de mais o nosso código, quando existem soluções mais simples e efetivas. Embora o foco maior do KISS seja para o momento onde você está transformando os requisitos do cliente em componentes implementáveis, também é possível aplicar esse conceito no momento do desenvolvimento do código. Você pode acabar achando KISS e DRY um pouco contraditórios, mas cabe pensar que ambos têm o propósito de diminuir a complexidade do software. Enquanto DRY reduz a complexidade dividindo as responsabilidades em componentes menores, o objetivo do KISS é reduzir a complexidade implementado a solução da forma mais simples possível.

#### Leitura recomendada

[3 princípios do desenvolvimento de software que você precisa entender.](https://code.tutsplus.com/tutorials/3-key-software-principles-you-must-understand--net-25161)

#### Referências

[http://www.slideshare.net/mariosangiorgio/clean-code-and-code-smells](http://www.slideshare.net/mariosangiorgio/clean-code-and-code-smells)
[https://code.tutsplus.com/tutorials/3-key-software-principles-you-must-understand--net-25161](https://code.tutsplus.com/tutorials/3-key-software-principles-you-must-understand--net-25161)
[https://classes.soe.ucsc.edu/cmps020/Winter09/lectures/refactoring-example.pdf](https://classes.soe.ucsc.edu/cmps020/Winter09/lectures/refactoring-example.pdf)

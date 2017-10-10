---
layout: post
date: 2016-10-16T13:00:00-03:00
title: "[C#] – Performance em aplicações de alto acesso – Memory Pool"
author: guilhermepohlmann
abstract: >
  Entendendo o Garbage Collector e utilizando o Memory Pool 
---

Quando nos deparamos com problemas de performance em nossas aplicações, normalmente a primeira opção é rever acesso a banco de dados e recursos de IO. Acontece que, para aplicações Web com alto acesso, um código pouco otimizado pode ser a causa de problemas de performance. Nesse artigo, vamos cobrir algumas optimizações de código que podem ser feitas para evitar esses problemas. 

Existem dois tipos de otimizações, **macro** e **micro** otimizações.

- Macro são aquelas otimizações que tem grande impacto na rotina sendo otimizada, geralmente segundos de diferença, dependendo o que está sendo executado.
- Micro são aquelas otimizações que nos garantem alguns milésimos de segundo, ou até mesmo nano segundos a menos na rotina, mas que devido a grandes quantidades de execução da rotina, acabam sendo de grande influência.

Nesta parte, vamos cobrir uma macro otimização, que trata de reaproveitamento de memória.  Grande parte dos problemas de performance em rotinas que executam muitas e muitas vezes ocorrem devido ao GC (*Garbage Collector*). Eu sei, atualmente um dos melhores amigos do programador, pode se tornar um grande vilão!

### Entendendo o GC

#### A divisão da Heap

Para entender melhor a técnica de reaproveitamento de memória (ou **Memory Pool**), vamos primeiro entender o problema, ou seja, entender como funciona o GC. Para essa parte, vou assumir que o leitor conhece o conceito de **Memory Heap**.

O GC divide a Heap em gerações, chamadas de **gen0**, **gen1** e **gen2** para objetos de curta duração, e **gen3**, também chamada de **LOH (Large Object Heap)** para objetos de vida longa e tamanho grande (em bytes).

Nós podemos imaginar essas gerações como grandes blocos de memória, onde o GC vai alocando objetos. Basicamente, qualquer **Reference Type** sempre será alocado na Heap, isto é, strings, arrays e classes definidas pelo programador.
<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-10-16-csharp-performance-em-aplicacoes-memory-pool/1.jpg" />
</center>

Existem diversos cálculos heurísticos que determinam onde um objeto será alocado, mas normalmente, o objeto inicia sua vida na **gen0**. 

Diversos fatores podem disparar uma coleta pelo GC, entre eles:

- Uma geração está ficando sem espaço.
- A aplicação está prestes a ficar sem memória disponível para alocação.
- A memória está muito fragmentada.
- O programador fez uma chamada ao método System.GC.Collect().

#### Ok, mas e dai?

Bom, acontece que quando o GC resolve mostrar o ar da sua graça, muita coisa pode acontecer na sua aplicação.

Existem várias fases da coleta que ocorrem quando o GC é disparado, dentre elas: **Mark Phase, Plan Phase, Realocate Phase, Compact Phase e Sweep Phase.**

Resumidamente, ele precisa calcular quais gerações devem ser coletadas e vasculhar todos os objetos dessas gerações para descobrir quem deve ser coletado e quem deve ser promovido a uma próxima geração. Além disso, o GC sempre vai preferir manter os blocos de memória desfragmentados, isto é, ele procura evitar “espaços vazios” na memória, movendo os objetos para estes espaços e sempre mantendo uma linha continua de alocação na memória.

A imagem abaixo demonstra uma situação onde o GC possivelmente efetuaria uma coleta, seguido de uma desfragmentação da Heap.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-10-16-csharp-performance-em-aplicacoes-memory-pool/2.png" />
</center>

Se você já programou em C/C++ meu amigo, já deve imaginar que para fritar todos esses ovos aconteceram muitos “malloc”, “realloc”, “memmove” e “free”.

Bom, essa altura você deve ter entendido que uma coleta custa caro, mas pode estar pensando, “ahhh, mas é só ele fazer isso em outra Thread que está tudo certo! ”, pois é, isso não acontece. Para conseguir realizar uma coleta, o GC suspende **TODAS** as Threads que estão rodando, pois ele precisa garantir que a memória não será alterada durante esse período (além de outros fatores **low level**), em outras palavras, o seu IIS não vai responder enquanto acontece a coleta.

Com isso podemos concluir que: quanto mais memória você alocar, menos responsiva será sua aplicação, por que eventualmente o mundo vai parar para a coleta. É claro que estamos falando de quantidades enormes de memória aqui.

### Utilizando reaproveitamento de memória

Uma técnica que vem sendo muita adotada pela comunidade C# é o reaproveitamento de memória, também chamado de **Memory Pool**. Essa técnica consiste em utilizar um objeto gerenciador, que realiza a alocação dos objetos na Heap, e sempre que a aplicação necessita utilizar determinado objeto, solicita ao gerenciador, quando a aplicação termina o uso, devolve ao gerenciador, como um sistema de aluguel.

Como exemplo, vamos utilizar uma aplicação WebAPI, com uma rotina que gera algum arquivo e depois disponibiliza para download. Nesse caso, cada vez que um usuário requisitasse essa API, no melhor caso, teríamos dois arrays de bytes na memória  (um para geração e um para download), que, dependendo do tamanho do arquivo gerado, podem ser enormes.

Nesse caso, teremos um **Memory Pool** de **byte[]**, ou seja, seriam alocados diversos arrays de bytes na memória, e cada vez que a aplicação precisa utilizar um array, ela aluga do pool e devolve quando terminar. Desse jeito, não acontecem milhares e milhares de alocações de arrays, pois já estão alocados e apenas são reutilizados. Outra técnica que os pool’s utilizam é a técnica de “pinned memory”, isto é, quando eles alocam a memória, eles sinalizam ao GC que essa memória não deve ser movida, o que diminui a pressão em cima do GC em uma coleta.

Um ponto importante que deve ser cuidado, é que, ao solicitar um array para o pool, você deverá informar o tamanho, mas o pool pode acabar devolvendo um array de tamanho MAIOR que o solicitado, por isso, tenha cuidado na utilização.
Um MemoryPool muito eficiente, que tem sido utilizado no ASP .NET Core, pode ser encontrado na biblioteca **System.Buffers**, disponível no [Nuget](https://www.nuget.org/packages/System.Buffers/).

No **WebAPI**, quando você responde com o método “**ResponseMessage**”, na propriedade “**Content**” da mensagem, você pode passar uma instância da classe “**ByteArrayContent**” como parâmetro, porém com isso você não tem nenhum jeito de saber se o download foi concluído para que possa devolver o array ao pool. Para isso, você pode criar uma classe muito simples, como demonstrado abaixo. O método “**Dispose**” será chamado quando o download for concluído, e nesse momento, você pode devolver seu buffer para o pool.


	internal sealed class BufferedByteContent : HttpContent
    {
        private readonly byte[] content;
        private readonly int offset;
        private readonly int count;
        private readonly ArrayPool<byte> pool;

        public BufferedByteContent(byte[] content, int offset, int count, ArrayPool<byte> pool)
        {
            if (content == null)
            {
                throw new ArgumentNullException(nameof(content));
            }

            this.content = content;
            this.offset = offset;
            this.count = count;
            this.pool = pool;
        }

        protected override Task SerializeToStreamAsync(Stream stream, TransportContext context)
        {
            return stream.WriteAsync(content, offset, count);
        }

        protected override Task<Stream> CreateContentReadStreamAsync()
        {
            return Task.FromResult<Stream>(new MemoryStream(content, offset, count, writable: false));
        }

        protected override bool TryComputeLength(out long length)
        {
            length = count;
            return true;
        }

        protected override void Dispose(bool disposing)
        {
            if (pool != null)
            {
                pool.Return(content, true);
            }
            base.Dispose(disposing);
        }
    }

O método abaixo é um exemplo de como ler um arquivo em um array de bytes alocados pelo pool. O método retorna os bytes do arquivo e a quantidade exata de bytes lidos, visto que o buffer retornado pode ser maior que o arquivo de fato.

	public static byte[] ReadBytes(string fileName, out int bytesRead)
	{
		using (FileStream fileStream = new FileStream(fileName, FileMode.Open, FileAccess.Read))
		{
			//Assume que o arquivo nunca vai ser maior que 2 GB
			bytesRead = (int)fileStream.Length;
			int count = bytesRead;
			int index = 0;
			byte[] buffer = ArrayPool<byte>.Shared.Rent(count);

			while (count > 0)
			{
				int n = fileStream.Read(buffer, index, count);
				index += n;
				count -= n;
			}

			return buffer;
		}
	}
	
#### Quanto utilizar

Essa técnica é muito utilizada em desenvolvimento de jogos, para manter arrays de bytes em memória, imagens e efeitos visuais. Também é utilizada em aplicações que precisam do máximo de performance possível, como por exemplo WebServers, e vem sendo utilizada no Web Server oficial pra ASP .NET Core, escrito em C# e com 20x mais performance que o ASP .NET normal rodando no IIS, chamado [Kestrel](https://github.com/aspnet/KestrelHttpServer). 

Você também pode utilizar essa técnica quando:

1.	Você está alocando muitos objetos e com muita frequência. Por exemplo, uma API Web com milhares de acessos.

- Aqui vale notar que apenas uma API pode não ser a causa de um problema de performance, mas a combinação de várias. Por exemplo, imagine que você tem uma API que a cada chamada aloca 50 objetos, e outra que a cada chamada aloca 30. Pode não parecer muito, mas se ambas forem muito utilizadas, você terá coletas frequentes.
- Também é importante notar que, você tem controle (e sabe) os objetos que está alocando. Mas você sabe todas as alocações que são feitas pelas bibliotecas que você utiliza? Ou Até mesmo pelo próprio framework? Talvez seja necessário otimizar a sua aplicação para compensar outros lados com problema, onde você não pode atuar. Afinal, você não pode reduzir as 100 alocações que o framework faz por chamada, mas pode reduzir as SUAS 50.

2.	Objetos terão tamanho similar.
3.	A alocação de objetos ou arrays poderá causar fragmentação da Heap.

#### Keep in mind

Normalmente você confia no GC para gerenciar a memória da sua aplicação. Utilizando um pool você está dizendo: “Eu sei melhor que você como esses bytes devem ser tratados”. Então será sua responsabilidade lidar com qualquer inconveniente que venha a acontecer.

#### Referências

[http://gameprogrammingpatterns.com/object-pool.html](http://gameprogrammingpatterns.com/object-pool.html)
[http://www.codeproject.com/Articles/39246/NET-Best-Practice-No-2-Improve-garbage-collector](http://www.codeproject.com/Articles/39246/NET-Best-Practice-No-2-Improve-garbage-collector)
[http://www.poppastring.com/blog/ASPNETCoreKestrelTheNeedForSpeed.aspx](http://www.poppastring.com/blog/ASPNETCoreKestrelTheNeedForSpeed.aspx)
[https://github.com/dotnet/coreclr/blob/master/Documentation/botr/garbage-collection.md](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/garbage-collection.md)

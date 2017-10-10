---
layout: post
date: 2017-02-05T00:58:56-02:00
title: ".NET - Threads, Tasks e o tal do ThreadPool"
author: fabriciorissetto
---

Certa vez em um projeto precisei desenvolver uma espécie de “worker” distribuído que tinha como função realizar duas simples tarefas:

- Obter um número X (1000, por exemplo) de eventos através de uma queue
- Sincronizar esses eventos com um Web Service remoto (cada uma dessas sincronizações podia demorar até 1 minuto).

Para isso eu primeiramente pensei em utilizar o método `Parallel.Foreach` que havia lido ser uma implementação do framework para facilitar tarefas de processamento paralelo. Ora, fazia sentido, não? Eu tinha 1000 eventos em que tudo o que eu precisava fazer era chamar um Web Service e ficar esperando o retorno. 

O código, de uma maneira simplificada, era próximo desse ([gist completo aqui]( https://gist.github.com/fabriciorissetto/69cfb8188cbab6c61eed30b6f814cdbf)):

    var listaEventos = Enumerable.Range(1, 1000);

    Parallel.ForEach(listaEventos,
        (idEvento) =>
    {
        Console.Write(".");
        Thread.Sleep(DoisSegundos); //Chamada para um Web Service
    });

Entretanto ao executar o código descobri que as coisas não estavam saindo exatamente como eu esperava:

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-02-03-threadsetasks/parallel.gif" />
</center>

Porque diabos ele está executando as tarefas num paralelismo tão baixo!? Algo como de 4 em 4... Eu queria que ele executasse todas ao mesmo tempo!

Então tive a magnífica ideia de abrir manualmente 1000 daquelas tal de tasks ([gist completo aqui]( https://gist.github.com/fabriciorissetto/ca78913af7cadc5abb6550e7aad34bee)):

    listaEventos.ToList().ForEach((idEvento) =>
    {
        Task.Run(() =>
        {
            Console.Write(".");
            Thread.Sleep(DoisSegundos); //Chamada para um Web Service
        });
    });

E... 

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-02-03-threadsetasks/parallel.gif" />
</center>

Powww! O resultado foi exatamente o mesmo. Mas por que!?

## Thread Pool
Os dois exemplos de código acima têm algo em comum: **eles utilizam o thread pool**.

O Thread Pool é uma implementação do framework que visa evitar o overhead da criação excessiva de threads. Ele resolve isso através da utilização de um “pool”. Nesse pool de threads ele administra quais delas irão executar em paralelo (normalmente esse número reflete a quantidade de núcleos do processador) e também faz um reaproveitamento delas pois quando uma tarefa conclui ele não elimina a thread, apenas marca ela como "livre e pronta para ser utilizada novamente".

## Thread
Ele faz isso porque o processo de criação e destruição de threads é extremamente custoso, envolvendo tarefas como: chamadas de sistema operacional, alocação de um bloco de memória grande, desalocação desse bloco de memória (no momento de matar a thread), etc. 

Além disso há um outro problema relacionado a threads: o conceito de **processamento paralelo na maioria das vezes é uma mentira**. Isso mesmo. Ele existe desde antes de termos processadores multicores. Como? O SO na esmagadora maioria das vezes (em TODAS as vezes para processadores single core) não executa as tarefas em paralelo de fato, ele executa um pouco de uma tarefa, para, executa outra, para, executa outra, para, e assim por diante. O nome dado a esse “para e vai pra outra” é [context switch]( https://en.wikipedia.org/wiki/Context_switch), algo extremamente custoso para a CPU. Quando mais threads, mais context switches. 

Mas veja bem, isso não quer dizer que threads são ruins. Na verdade há casos em que é melhor utilizar elas do que a engine do thread pool. 

#### Em resumo, e já adiantando, a regra básica é:

- Utilize o **Thread Pool** para tarefas ***"CPU Bound"***. Exemplos: cálculos hash, operações em matrizes, algoritmos recursivos, loops que utilizam grandes quantidades de variáveis locais, operações de reflection, etc).
- Utilize **Threads** para tarefas ***"I/O Bound"***. Exemplos: Leituras ou escritas em arquivos no disco, chamadas para Web Services ou APIs, escritas em banco de dados, testes de carga simulando requisições a uma aplicação Web, etc).

Veja bem, a regra é simples de entender: se a tarefa é CPU bound, ou seja, a maior carga de trabalho dela está em processamento de instruções no processador, não faz muito sentido eu abrir 5 threads diferentes para fazer esses processamentos sendo que eu tenho apenas 4 núcleos no meu CPU. A thread que sobrou está sedenta por um CPU inteirinho só pra ela.

Já para o outro caso, onde minha tarefa é resource bound, não faz muito sentido também eu abrir apenas 4 threads devido a preocupação de não atolar meu CPU, sendo que minha tarefa é apenas chamar um Web Service e deixar minhas threads assim:

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-02-03-threadsetasks/bored.png" />
</center>

## Back to the problem

Sabendo disso, ficou claro para mim que o que eu precisava eram threads. No final das contas acabei com um código muito próximo desse ([gist completo aqui](https://gist.github.com/fabriciorissetto/96b9db06f17bd10f4fdd0ab969a83845)):

    var quantidadeThreads = 100;
    
    for (int i = 0; i < quantidadeThreads; i++)
    {
        new Thread(new ThreadStart(() =>
        {
            //Infinitamente aberta, sempre sincronizando eventos...
            while (true)
            {
                ObtemEventoDaFilaESincronizaComWebService();
            }
        })).Start();
    }

Agora sim o negócio tava funcionando como eu queria:

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-02-03-threadsetasks/threads.gif" />
</center>

Observem que também modifiquei a lógica do algoritmo. Ao invés de obter 1000 eventos da fila, criar 1000 threads pra sincronizá-los e em seguida destruí-las, eu optei por criar uma quantidade menor de threads (aproximadamente 100) e deixá-las abertas durante toda a execução do programa. <del>Poderia chamar essas 100 threads de "Pool" só pra confundir o post.</del> O conteúdo dessas threads é que era responsável por: obter o último evento da fila, sincronizá-lo.

Como cheguei nessa quantidade ideal de threads? Testes. Testando os limites da máquina descobri que com 1500 threads dava OutOfMemoryException, e que aproximadamente 500 já era suficiente pra deixar o CPU no talo (context switches).

## Tasks

O segundo código que exemplifiquei no início do post utilizava tasks, mais especificamente o método `Task.Run()`. Conforme comentei, tasks por baixo dos panos utilizam o thread pool, por isso é indicado que sejam utilizadas para tarefas *CPU Bound*. Entretanto, há uma opção que nos permite informar que aquela task será "demorada", isso faz com que a engine (na implementação atual) crie uma nova thread ao invés de utilizar uma do pool:

    Task.Factory.StartNew(() =>
    {
        //Chamada do Web Service
    }, TaskCreationOptions.LongRunning);

O código acima tem o mesmo efeito prático deste daqui:

    new Thread(new ThreadStart(() =>
    {
        //Chamada do Web Service
    })).Start();

Entretanto utilizar tasks (mesmo as long running) traz algumas facilidades para o desenvolvimento, tais como:

1. Tasks podem retornar resultados. Threads não possuem um mecanismo direto pra isso.
2. Com o Wait é possível esperar por várias tasks sem precisar construir um mecanismo de sinais.
3. Tasks podem ter sua execução encadeada com facilidade.
4. Exceptions em tasks filhas propagam para tasks pais.
5. Tasks suportam cancelamento de maneira simples via token de cancelamento.

Sendo assim, é possível utilizar tasks tanto para tarefas CPU bound quanto Resource bound **contanto que se configure corretamente a opção "LongRunning" para esse segundo caso**.  

Mas não se esqueça, quando se fala em paralelismo normalmente estamos visando performance. Quando se trata de performance não podemos trabalhar com suposições. Nada melhor do que fazer testes, comparações e análises de comportamento do código para tirar conclusões se a solução está atendendo devidamente ao problema.
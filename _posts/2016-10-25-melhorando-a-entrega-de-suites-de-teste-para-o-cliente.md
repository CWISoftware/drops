---
layout: post
date: 2016-10-25T13:00:00-03:00
title: "Melhorando a entrega de Suítes de Testes para o cliente"
author: josebarbosa
abstract: >
  Agilizando o processo de documentação e entrega de testes  
---

Em uma *Software House* sempre buscamos otimizar o máximo possível nosso tempo disponível, visando ter maior eficiência dentre as horas destinadas ao desenvolvimento do projeto.  Existem diversos processos que se tornam gargalos ao longo do tempo, seja por atrasos, mudanças repentinas de escopo, inconformidades detectadas de última hora, etc. Dentre estes está a escrita, execução e entrega das Suítes de Testes, que atestam a qualidade do produto entregue.

Para agilizar o processo, podemos utilizar ferramentas como o MTM (*Microsoft Test Manager*) ou *Testlink*, por exemplo. Entretanto, é necessário atentar aos requisitos de entrega do cliente, que podem facilitar ou dificultar o processo de qualidade.

Em um cenário recente, utilizamos o *Testlink* como ferramenta de escrita de testes devido à solicitação do cliente, pois este, apesar de utilizar o TFS (Team Foundation Server) e o MTM para o desenvolvimento e controle de qualidade, recebe de outros fornecedores arquivos exportados pelo *Testlink* no formato XML e faz a importação dos mesmos.

A equipe de desenvolvimento utiliza o TFS para o controle de versão do projeto, o que dificulta a rastreabilidade (*Traceability*) e a criação de automações vinculadas às Suítes de Teste. Dessa forma, o processo de garantia de qualidade acaba tornando-se um gargalo, pois demanda mais testes manuais e dificulta a identificação e resolução de inconformidades.

Para melhorar este processo, a alternativa mais eficiente é migrar a escrita de testes para o MTM, então inicialmente desenvolvemos uma ferramenta que, utilizando a API do TFS, faz a importação de uma Suíte de Testes de um Plano de Testes para outro, estando eles no mesmo servidor ou não (para tal é necessário ter usuário com permissões em ambos os servidores).

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-10-25-melhorando-a-entrega-de-suites-de-teste-para-o-cliente/1.png" />
</center>
<center>
*Tela de seleção de suítes do TFS2TFS.*
</center>

Como o processo de implantação de funcionalidade nova no cliente pode levar algum tempo, implementou-se também outra solução. Desenvolveu-se uma ferramenta que exporta as Suítes criadas no MTM em formato XML compatível com o Testlink. 

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2016-10-25-melhorando-a-entrega-de-suites-de-teste-para-o-cliente/2.png" />
</center>
<center>
*Tela do TFS2Testlink com a listagem de Casos de Teste para exportar em XML.*
</center>

Com as novas soluções, é possível realizar a escrita de cenários no MTM, o que permite maior integração entre o desenvolvimento e o controle de qualidade e mantem as entregas como o cliente deseja.

As ferramentas desenvolvidas (utilizando a linguagem C#) foram disponibilizadas no Git CWI, para que outras equipes possam utilizá-la e também contribuir com melhorias.
- [TFS2TFS](http://git.cwi.com.br/jose.barbosa/TFS2TFS)
- [TFS2Testlink](http://git.cwi.com.br/jose.barbosa/TFS2Testlink)

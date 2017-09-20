---
layout: post
date: 2016-02-17T12:00:00-02:00
title: "Integração Contínua na CWI"
author: alexandre-machado
abstract: >
  Integração Contínua na CWI, um resumo.
---
Em fevereiro de 2015 [Altamir](https://www.facebook.com/altamir.junior.dias) e eu apresentamos um resumo sobre integração contínua,
seus benefícios, adoção em projetos da CWI e a percepção de retorno destes projetos.

Ferramentas como Jenkins, TeamCity e TFS já eram muito utilizadas: 

<center>
  <img alt="Ferramentas mais utilizadas" src="{{ site.baseurl }}/content/2016-02-17-integração-contínua-na-cwi/ambientes.png" />
</center>

Naquela ocasião, listamos clientes como **Unimed POA**, **Lojas Renner**, **TNT Express**, **Casas Bahia** e **Coca-Cola**.

Os depoimentos foram variados mas animadores:

#### Lojas Renner:
> "Conseguimos manter uma qualidade e muito boa cobertura de testes, mas ainda há espaços para melhorar." - Gustavo Jotz

#### TNT Express:
> "Atingimos o objetivo esperado com a utilização." - Lucas Balensiefer

#### Casas Bahia:
> "Conseguimos delegar para a fabrica de testes os triggers de deploy para os ambientes. Eles conseguem verificar as features que estão entrando em cada deploy. E consigo fazer um deploy em produção com tranquilidade sabendo que o build passou por todos os níveis do pipeline." - Daniel Wayhs

#### Coca-Cola:
> "O processo só não esta melhor por que a ferramenta do cliente (uDeploy) exige alguns processos manuais." - Jonas Flesch

Confira os slides da apresentação na íntegra:

<center>
  <iframe src="//pt.slideshare.net/slideshow/embed_code/key/3T7JlN5D5hoxjE" width="740" height="603" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>
</center>
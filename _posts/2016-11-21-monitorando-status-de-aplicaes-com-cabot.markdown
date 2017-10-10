---
layout: post
date: 2016-11-21T20:40:33-02:00
title: "Monitorando Status De Aplicações Com Cabot"
author: rafaeleyng
abstract: Como configurar um *status check* para suas aplicações
---

Cabot é um serviço de monitoramento de aplicação. Basicamente é um sistema que você publica na sua própria infraestrutura e configura para verificar as URLs da sua aplicação em um dado intervalo, e possivelmente habilita algum alerta para quando algo cair.

Assim:

![screen shot 2016-11-19 at 11 23 48 pm](https://cloud.githubusercontent.com/assets/4842605/20459783/9930e04a-aeb5-11e6-8fed-1ab2aa866d95.png)

Vários serviços oferecem funcionalidade semelhante ([Pingdom](https://www.pingdom.com/), [Uptime Robot](https://uptimerobot.com/)), mas Cabot se diferencia por ser self-hosted, open-source e gratuito, então você não precisa se preocupar com constraints como um limite mínimo de intervalo, ou um número máximo de aplicações que você possa monitorar.

Esse é um post básico que mostra como rodar e usar Cabot o mais rápido possível. Vou mostar apenas uma forma de fazer cada coisa.

## Instalação

Instale com Docker, usando [cabot-docker](https://github.com/shoonoise/cabot-docker):

1. instale docker + docker-compose.

1. `git clone https://github.com/shoonoise/cabot-docker.git`

1. `cd cabot-docker` e olhe o arquivo `cabot_env`. Diversas variáveis de configuração de alertas e integrações são definidas aqui. Comente todas para não ter valores inválidos depois, e volte aqui se precisar.

1. `docker-compose up -d`

O serviço irá subir na porta 8080 (altere no docker-compose.yml se quiser). As credenciais default são docker/docker.

## Conceitos

3 conceitos principais no Cabot: check, instance and service.

**Check** é uma tarefa particular que você quer rodar para verificar algo. Checks podem ser de alguns tipos pré-definidos, como:
  - ping: um ping para um host
  - HTTP: chame uma URL e verifique o HTTP status

**Instance** é uma instância real de uma máquina (virtual ou física) que terá algum serviço rodando. Uma instância terá um IP/hostname.

**Service** é a coisa mais macro que você quer monitorar. Funciona como um agregador de instâncias e checks.

A documentação é bastante obscura sobre esses conceitos, então aqui está a minha opinião sobre como usá-los.

## Configurando

Existe um relacionamento N-N entre essas 3 entidades, então a organização e relacionamento entre tudo pode ser bastante confusa. Esse post é bastante opinionado sobre como fazê-lo.

Você pode ter um **service** com múltiplas **instances**, e múltiplas **checks** apontando para cada instância.

Eu uso Cabot para monitorar tanto nossos servidores de front-end quanto nossa API, de múltiplas aplicações, rodando cada uma em múltiplos hosts.

Eu começo criando **instances**. Cada instância criada terá um IP/hostname, e irá criar automaticamente uma check de ping para a instância. Para cada instância, eu crio uma check HTTP que verifica o HTTP status e algum match de texto no retorno (no HTML, por exemplo). Por fim, crio um **service** e agrupo nele todas suas instâncias.

## Automatizando

Clicar é lento e propenso a erros.

Infelizmente, Cabot não tem uma REST API que possamos usar para configurar nossos serviços automaticamente.

Atualmente vejo 2 opções para automatizar:

- [Edit 23/12/2016: deprecated! Veja cabot-db-config abaixo] eu criei o [cabot-zombie](https://github.com/rafaeleyng/cabot-zombie), que roda um *headless browser* e configura Cabot baseado em um objeto Javascript contendo as configurações. Olhe o projeto para mais detalhes.

- acesse a instância Postgres que o Cabot usa e faça alguns inserts diretamente, automatizando isso da forma que preferir. As tabelas envolvidas são:
  - cabotapp_instance_status_checks
  - cabotapp_instance
  - cabotapp_instancestatussnapshot
  - cabotapp_service_instances
  - cabotapp_service_status_checks
  - cabotapp_service
  - cabotapp_servicestatussnapshot
  - cabotapp_statuscheck
  - cabotapp_statuscheckresult

- Eu criei [cabot-db-config](https://www.npmjs.com/package/cabot-db-config), que usa uma configuração similar ao cabot-zombie, mas é bem mais rápido e confiável, porque conecta diretamente ao banco de dados do seu Cabot para inserir a configuração.

O que eu uso é cabot-db-config para criar tudo, e se quiser mudar alguma coisa, deleto tudo das tabelas e rodo novamente o cabot-zombie com minhas alterações.

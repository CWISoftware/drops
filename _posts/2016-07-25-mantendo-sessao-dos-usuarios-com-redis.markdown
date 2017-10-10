---
layout: post
date: 2016-07-25T09:53:21-03:00
title: "Mantendo a sessão dos usuários com Redis"
author: eskeff
abstract: >
  Mantendo a sessão dos usuários com Redis
---

A publicação de uma nova versão em produção de sistemas despreparados, fatalmente acarreta na “derrubada” de todos os nossos usuários conectados. E isso ocorre já que as sessões dos usuários por padrão ficam armazenadas no servidor de aplicação. 

Por isso, o melhor horário para essa ação, geralmente a madrugada, precisa sempre ser pauta desse rotineiro processo.

Mas se for um sistema crítico, de alta disponibilidade?  Não teríamos um horário ideal para publicar uma nova versão, somente um horário de menor impacto possível para os usuários.

Então precisamos de uma solução para não ter que fazer o usuário autenticar-se novamente e perder todo seu trabalho.

A solução proposta para esse cenário é utilizar o [Redis](http://redis.io) e ela pode ser aplicada em qualquer sistema que precise armazenar as sessões do usuário.

Nesse Drops irei utilizar ele em conjunto com o framework [Spring Boot](http://projects.spring.io/spring-boot) para Java.

# O que é Redis?

Em suma é um banco não relacional em memória, que pode ser usado como cache, mensageria ou como nesse exemplo guardar as sessões dos usuários.

# Construindo uma aplicação de exemplo com Spring Boot

Vamos construir uma aplicação com uma camada simples de segurança de exemplo, para isso precisaremos de:

```
•	Uma IDE, no caso será utilizado Eclipse;
•	Java JDK 1.6
•	Maven 3.0


1)	Clonar o projeto https://github.com/eskeff/spring-redis;
2)	Importar no Eclipse como Maven Project;
3)	Executar a classe App.java;

```

Feito, temos uma aplicação web com segurança rodando, acesse http://localhost:8080/hello e verá uma página parecida com essa:

 ![image](https://github.com/eskeff/images/blob/master/1_login.png?raw=true)

 
Acesse usando as credencias “user@123”, e terá acessado a página:

 ![image](https://github.com/eskeff/images/blob/master/2_hello.png?raw=true)
 
Nesse ponto se derrubarmos o servidor de aplicação e subir novamente iremos perder a sessão, para que isso não ocorra vamos seguir com o Drops.

# Instalar o Redis com Docker 

O Redis pode ser facilmente instalado no [Windows](https://github.com/rgl/redis/downloads) ou [Linux](http://redis.io/topics/quickstart) 
Mas para mantermos um padrão iremos utilizar o  [Docker](https://www.docker.com/) para facilitar nossas vidas :).

**Windows**

Baixe  a versão do seu [Windows](https://docs.docker.com/docker-for-windows/)  e instale normalmente com as configurações padrão.

**Linux**

Escolha sua distribuição [Linux](https://docs.docker.com/engine/installation/linux/)  e instale normalmente com as configurações padrão.

Feito isso o comando `docker` já deve funcionar.

Após, simplesmente rode o comando:
`docker run --name meu-redis -d -p 6379:6379 redis redis-server` e espere alguns instantes, após finalizar rode o comando `docker ps`:
 
  ![image](https://github.com/eskeff/images/blob/master/3_docker.png?raw=true)

Com isso o Redis já está rodando na sua máquina, no meu caso no localhost na porta 6379.

Execute `docker exec -it meu-redis redis-cli` para conseguirmos acessar o Redis:

  ![image](https://github.com/eskeff/images/blob/master/4_redis.png?raw=true)

# Voltando a aplicação  

Então vamos voltar para o nosso projeto.
Adicione a dependência no pom.xml:

```
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-redis</artifactId>       
</dependency>
```
E retire todos os comentários da classe HttpSessionConfig.java:
 
   ![image](https://github.com/eskeff/images/blob/master/5_eclipse.png?raw=true)

Agora basta rodar a aplicação novamente e após logar:

  ![image](https://github.com/eskeff/images/blob/master/2_hello.png?raw=true)
 
 E então execute `Keys *` no Redis e deve parecer:

   ![image](https://github.com/eskeff/images/blob/master/6_redis.png?raw=true)
 
Ok, a sessão está sendo armazenada no Redis


Agora podemos derrubar nossa aplicação:
 
  ![image](https://github.com/eskeff/images/blob/master/7_fail.png?raw=true)

E subi-la novamente e recarregar a página “http://localhost:8080/hello”:
  
![image](https://github.com/eskeff/images/blob/master/2_hello.png?raw=true)
 
**E continuamos autenticados, SUCESSO!!!**

Agora um último teste, vamos executar o comando `flushall` que removerá todos os registros no Redis:

  ![image](https://github.com/eskeff/images/blob/master/8_redis.png?raw=true)
 
E tentar acessar a página http://localhost:8080/hello novamente:

  ![image](https://github.com/eskeff/images/blob/master/1_login.png?raw=true)
 
E fomos redirecionados para a tela de login já que o Spring não tem mais a sessão anterior 


# Conclusão

Com essa pequena aplicação conseguimos demonstrar que podemos salvar as sessões de forma externa, facilitando a implantação de deploys de baixo risco como Blue-Green Deployment.
O próximo passo é utilizar o Redis em conjunto com o Redis Sentinel na configuração Master e Slave para garantirmos uma alta disponibilidade das sessões.

Até a próxima.

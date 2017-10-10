---
layout: post
date: 2016-12-01T21:00:51-02:00
title: "Usando Nexus 3 Como Seu Repositório - Parte 1: Artefatos Maven"
author: rafaeleyng
excerpt: >
  Vamos instalar Nexus 3 e configurá-lo para usá-lo como repositório privado Maven e como proxy para o Maven Central e outros repositórios
---

<small>
Essa é a primeira parte de uma série de posts sobre Nexus 3 e como usá-lo como seu repositório para diversas tecnologias.
</small>

## Instalação

Instale com Docker:

```
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name my-nexus sonatype/nexus3:3.0.0
```

<small>
Estamos mapeando todas essas portas (8081-8083) por causa dos próximos posts da série. Para esse post, só precisaremos da porta 8081.
</small>

Nexus 3 subirá na porta 8081. Credenciais default são admin/admin123.

Pode ser interessante você criar um volume para mapear a pasta de dados do Nexus para seu host, adicionando a opção `-v /opt/my-nexus-data:/nexus-data`.

## Configurando Nexus como repositório Maven

O que nós faremos:
  - criar um repositório privado (hosted) para nossos snapshots
  - criar um repositório privado (hosted) para nossos releases
  - criar um repositório proxy apontando para Maven Central
  - criar um repositório do tipo group, para prover todos esses repositórios em uma única URL

Eu sugiro você criar um novo blob store para cada novo repositório que criar. Assim os dados para cada repositório ficarão em uma pasta diferente em `/nexus-data` (dentro do container Docker). Mas isso não é obrigatório para funcionar.

### repositório de snapshots

Um repositório para artefatos Maven que você faz deploy **com** `-SNAPSHOT` no final da tag de versão no pom.xml:

```
<version>1.0.0-SNAPSHOT</version>
```

Crie um novo repositório maven (hosted) e configure assim:

![maven-snapshots0](https://cloud.githubusercontent.com/assets/4842605/20580349/f43cdad8-b1b8-11e6-8ff8-a9a02082197a.png)

### repositório de releases

Um repositório para artefatos Maven que você faz deploy **sem** `-SNAPSHOT` no final da tag de versão no pom.xml:

```
<version>1.0.0</version>
```

Crie um novo repositório maven (hosted) e configure assim:

![maven-releases0](https://cloud.githubusercontent.com/assets/4842605/20580348/f42e9964-b1b8-11e6-8e32-4a0dc717d7bf.png)

### repositório de proxy para Maven Central

Um repositório que age como proxy para o que você baixa do Maven Central. Da próxima vez que baixar a mesma dependência, estará cacheada no seu Nexus.

Crie um novo repositório maven (proxy) e configure assim:

![maven-central0](https://cloud.githubusercontent.com/assets/4842605/20580346/f40f4488-b1b8-11e6-8fce-33034ef14978.png)

![maven-central1](https://cloud.githubusercontent.com/assets/4842605/20580345/f40e387c-b1b8-11e6-8e4a-c314273bf1a0.png)

### repositório de agrupamento

Isso irá agrupar todos os repositórios acima e prover uma única URL, para configurar seus clientes para fazerem o deploy/download.

Crie um novo repositório maven (group) e configure assim:

![maven-group0](https://cloud.githubusercontent.com/assets/4842605/20580347/f427ce5e-b1b8-11e6-8a93-52cda1f49f59.png)

Você pode criar todos repositórios que precisar (como proxies para outros repositórios públicos) e agrupá-los sob o mesmo repositório do tipo group.


## Configurando clientes e projetos para usar seu Nexus

Coloque isso no seu arquivo `~/.m2/settings.xml`. Isso irá configurar as credenciais de publicação para seus repositórios *hosted*, e dirá ao seu `mvn` para usar seu repositório como um mirror do Maven Central:

```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">

  <servers>
    <server>
      <id>nexus-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>nexus-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
  </servers>

  <mirrors>
    <mirror>
      <id>central</id>
      <name>central</name>
      <url>http://your-host:8081/repository/maven-group/</url>
      <mirrorOf>*</mirrorOf>
    </mirror>
  </mirrors>

</settings>
```

E agora configure seus projetos.

Se você só quer baixar dependências do Nexus, coloque isso no `pom.xml`:

```
<project ...>

  ...

  <repositories>
    <repository>
      <id>maven-group</id>
      <url>http://your-host:8081/repository/maven-group/</url>
    </repository>
  </repositories>
</project>
```

E se você quer também publicar seus projetos para o Nexus, adicione:

```
<project ...>

  ...

  <distributionManagement>
    <snapshotRepository>
      <id>nexus-snapshots</id>
      <url>http://your-host:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
    <repository>
      <id>nexus-releases</id>
      <url>http://your-host:8081/repository/maven-releases/</url>
    </repository>
  </distributionManagement>
</project>
```

Agora se você rodar em seus projetos:

```
mvn install
# ou
mvn deploy
```

seu `mvn` irá apontar para sua instância Nexus.

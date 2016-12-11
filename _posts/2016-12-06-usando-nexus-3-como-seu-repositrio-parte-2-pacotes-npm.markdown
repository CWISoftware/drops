---
layout: post
date: 2016-12-06T00:12:54-02:00
title: "Usando Nexus 3 Como Seu Repositório - Parte 2: Pacotes Npm"
author: rafaeleyng
excerpt: >
  Continuando com nossa configuração de Nexus 3, vamos aprender a usá-lo repositório privado npm e como proxy para o registro oficial do npm
---

<small>
Essa é a segunda parte de uma série de posts sobre Nexus 3 e como usá-lo como repositório para diversas tecnologias.
</small>

`npm install` pode demorar bastante, então pode ser uma boa ideia ter um proxy na sua própria rede. E se você não puder simplesmente pagar os 7 dólares por mês para hospedar seus pacotes no npm privado, então esse post provavelmente te interessa.

## Instalação

Veja na [primeira parte](http://cwisoftware.github.io/drops/usando-nexus-3-como-seu-repositrio-parte-1-artefatos-maven) desta série de posts como instalamos e rodamos Nexus 3 usando um único comando docker.

## Configurando Nexus 3 como repositório npm

O que nós faremos:
  - criar um repositório privado (hosted) para nossos próprios pacotes
  - criar um repositório proxy apontando para o registro oficial
  - criar um repositório do tipo group, para prover todos esses repositórios em uma única URL

Eu sugiro você criar um novo blob store para cada novo repositório que criar. Assim os dados para cada repositório ficarão em uma pasta diferente em `/nexus-data` (dentro do container Docker). Mas isso não é obrigatório para funcionar.

### repositório privado

Um repositório para pacotes npm que seu time desenvolve.

Crie um novo repositório npm (hosted) e configure assim:

![npm-private0](https://cloud.githubusercontent.com/assets/4842605/20909966/d6f8101e-bb45-11e6-9791-0f2472866fdd.png)

Se a política de deploy "Allow redeploy" acima parecer meio polêmica, então troque ela para "Disable redeploy". No meu caso de uso, faz sentido usar "Allow redeploy", já que mantemos uma versão `latest` no Nexus sempre atualizada com o status da branch master, que é recebe as atualizações no nosso fluxo de CI.

### repositório proxy

Um repositório que faz proxy de tudo que você baixa do registro oficial npm. Na próxima vez que você baixar a mesma dependência, estará no cache do seu Nexus.

Crie um novo repositório npm (proxy) e configure assim:

![npm-registry0](https://cloud.githubusercontent.com/assets/4842605/20909964/d6f6568e-bb45-11e6-9161-6e302ed1757f.png)

![npm-registry1](https://cloud.githubusercontent.com/assets/4842605/20909965/d6f7e9ea-bb45-11e6-86f1-c6ce957bf948.png)

### repositório de agrupamento

Isso irá agrupar todos os repositórios acima e prover uma única URL, para configurar seus clientes para fazerem o deploy/download.

Crie um novo repositório npm (group) e configure assim:

![npm-group0](https://cloud.githubusercontent.com/assets/4842605/20909963/d6f41e32-bb45-11e6-9134-848409b5d781.png)

Você pode criar todos repositórios que precisar (como proxies para outros repositórios públicos) e agrupá-los sob o mesmo repositório do tipo group.

## Configurando clientes e projetos para usar seu Nexus

Para npm, nós vamos fazer as configurações de repositórios em cada projeto (diferente do Maven, que tem algumas configurações globais, por exemplo). Eu acredito que você pode configurar a autenticação globalmente na sua máquina com `npm addUser`, mas eu não segui esse caminho por considerar mais simples a abordagem a seguir.

Se você tem um projeto onde você quer apenas fazer **download** de dependências do Nexus, crie um arquivo `.npmrc` na raíz do seu projeto com:

```
registry=http://your-host:8081/repository/npm-group/
_auth=YWRtaW46YWRtaW4xMjM=
```

`_auth=YWRtaW46YWRtaW4xMjM=` é o hash base64 para as credenciais (admin/admin123). Se você usa credenciais diferentes, compute seu hash com:

```
echo -n 'myuser:mypassword' | openssl base64
```

Você precisa setar um usuário para publicar pacotes. Se você publicar da sua máquina local, `npm publish` irá usar suas configurações de usuário do seu `~/.npmrc` (na sua home, não no seu projeto). Se você não tem essa configuração, ou quer fazer a publicação na integração contínua, você pode setar `email=qualquer@email.com` no `./npmrc` do projeto. Qualquer email serve, não precisa ser real.

Se você tem um projeto que você quer **publicar** para seu Nexus, coloque isso no `package.json`:

```
{
  ...

  "publishConfig": {
    "registry": "http://your-host:8081/repository/npm-private/"
  }
}
```

Note que você publica para seu repositório privado, mas quando faz download, pode apontar para seu repositório de agrupamento, de forma que tanto seus próprios pacotes quanto os do repositório oficial ficam disponíveis na mesma URL.

Agora se você rodar em seus projetos:

```
npm install
# ou
npm publish
```

seu `npm` irá apontar para sua instância Nexus.

## Instalando pacotes npm globalmente

Rode:

```
npm --registry http://your-host:8081/repository/npm-group/ install -g meu-pacote
```

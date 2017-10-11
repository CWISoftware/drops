---
layout: post
date: 2016-05-25T12:00:00-02:00
title: "Integração Contínua .NET + GitLab + GitLab CI"
author: alexandre-machado
abstract: >
  GiLab CI, uma opção gratuita e de código aberto para integração contínua na CWI
---
O [GitLab CI](https://about.gitlab.com/gitlab-ci/) é um software para integração contínua que faz parte do [GitLab](https://about.gitlab.com/), que por sua vez é um software de controle de versão baseado em Git.

Com GitLab CI podemos executar builds no Unix, Windows, OSX e qualquer outra plataforma que suporte Go.

# Integração Windows
A integração com o Windows fica por conta das opções de linha de comando via Bash, Windows Batch ou PowerShell:

| Shells  |    Bash    | Windows Batch | PowerShell |
|---------|:----------:|:-------------:|:----------:|
| Windows |      ✓     |   ✓ (padrão)  |      ✓     |
| Linux   | ✓ (padrão) |      não      |     não    |
| OSX     | ✓ (padrão) |      não      |     não    |
| FreeBSD | ✓ (padrão) |      não      |     não    |

# Configuração do servidor de CI
Vá até o endereço [http://ci-gitlab.cwi.com.br/](http://ci-gitlab.cwi.com.br/) e adicione seu projeto no CI:

![image](https://cloud.githubusercontent.com/assets/1766903/15524513/96dff518-21f9-11e6-8570-da8897272b45.png)

Na página do projeto, vá até **Runners** e copie o token:

![image](https://cloud.githubusercontent.com/assets/1766903/15524583/16d893a6-21fa-11e6-8c99-3d3272b78fc5.png)

# Instalação de Agente de Build no Windows
* Crie uma pasta no disco. (`c:\gitlab-ci`)
* Baixe o executável [x86](https://gitlab-ci-multi-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-ci-multi-runner-windows-386.exe) ou [amd64](https://gitlab-ci-multi-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-ci-multi-runner-windows-amd64.exe)
* Execute o prompt de comando em modo administrativo
* Execute os passos à seguir pelo prompt:

```
cd C:\gitlab-ci
gitlab-ci-multi-runner register

# Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/ci):
http://ci-gitlab.cwi.com.br
# Please enter the gitlab-ci token for this runner:
TOKEN-BUSCADO-DA-PÁGINA-DE-CONFIGURAÇÃO
# Please enter the gitlab-ci description for this runner:
[Alexandre-Note]: meu-runner
# Please enter the gitlab-ci tags for this runner (comma separated):
build-dev
# Registering runner... succeeded                     runner=37c2979b
# Please enter the executor: ssh, virtualbox, docker+machine, docker-ssh+machine, docker, docker-ssh, parallels, shell:
shell
# Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```

Você pode instalar seu *runner* como um serviço do windows:

```
gitlab-ci-multi-runner install --user COLOQUE-SEU-USUÁRIO --password COLOQUE-SUA-SENHA
gitlab-ci-multi-runner start
```

Seu novo *runner* irá aparecer na página de configuração do projeto:

![image](https://cloud.githubusercontent.com/assets/1766903/15524400/7e7ff302-21f8-11e6-80dd-dbdddf234683.png)

Para documentação oficial visite: https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/install/windows.md 

# Configuração 
Para a execução de build de projetos .NET, utilizamos o [MSBuild](https://msdn.microsoft.com/pt-br/library/ms164311.aspx) configurado em um arquivo no padrão [YAML](https://en.wikipedia.org/wiki/YAML) geralmente com o nome `.gitlab-ci.yml`.

Ex.: (http://stackoverflow.com/questions/32964953/gitlab-ci-and-msbuild-with-tests)

```
before_script:
  - 'call "%VS120COMNTOOLS%\vsvars32.bat"'
  - '".\src\.nuget\NuGet.exe" restore ".\src\CwiDojo.sln"'

build:
  script:
  - echo compilando... 
  - '"C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe" .\src\CwiDojo.sln'
  except:
  - tags
```

Na CWI, o GitLab é acessível pelo endereço [http://git.cwi.com.br/](http://git.cwi.com.br/) e o GitLab CI é acessível pelo endereço [http://ci-gitlab.cwi.com.br/](http://ci-gitlab.cwi.com.br/).

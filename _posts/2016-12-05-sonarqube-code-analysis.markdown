---
layout: post
date: 2016-12-05T12:00:00-00:00
title: "Análise de código com SonarQube"
author: giovanidecusati
abstract: Utilizando a ferramenta SonarQube para análise de código
---
Práticas de análise de código são fundamentais durante o processo de desenvolvimento de software. É comum nos projetos a utilização de ferramentas estáticas de análise de código para otimizar o processo de revisão.
O uso deste tipo ferramenta torna-se ainda mais poderoso quando alinhado a um processo de integração contínua, fazendo com que códigos de baixa qualidade sejam recusados.
O objetivo deste post é sugerir algumas ferramentas de análise de código, dando ênfase a ferramenta SonarQube.     

Sugestões de ferramentas:
- [SonarQube](http://www.sonarqube.org)
- [FxCop](https://www.microsoft.com/en-us/download/details.aspx?id=6544)
- [Nitriq](www.nitriq.com)
- [ReSharper](https://www.jetbrains.com/resharper)
- [JustCode](http://www.telerik.com/products/justcode.aspx)
- [NDepend](http://www.ndepend.com)

## SonarQube
É uma plataforma aberta para gerenciamento da qualidade do código. Oferece um conjunto de regras que cobre os principais aspectos da qualidade, sendo eles:
- Arquitetura e Design
- Comentários
- Regras de Código
- Potenciais Bugs
- Complexidade
- Testes Unitários
- Duplicações de Código

Pode ser usado das seguintes formas:
- Através de um plug-in instalado no VSTUDIO chamado [SonarLint](http://www.sonarlint.org/visualstudio/).
- Integração por linha de comando [Sonar-Scanner](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner).


### Sonar-Scanner
1. Utilize o Sonar-Scanner para enviar os dados de análise de código para o servidor do SonarQube. Pode ser feito manualmente por linha de comando ou integrado no seu processo de CI.

2. Acesse a página [Analyzing with SonarQube Scanner for MSBuild](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+MSBuild) e baixe o arquivo ZIP.

3. Descompacte o arquivo em algum diretório e inclua o diretório no PATH do Windows. Ex: "C:\sonar-scanner-msbuild-2.2.0.24\sonar-scanner-2.8\bin"

4. Adicione o arquivo sonar-project.properties no diretório raiz da aplicação conforme o exemplo:

<center style="margin: 20px">
  <img style="margin-top: 20px" src="{{ site.baseurl }}/content/2016-12-05-sonarqube-code-analysis/sonar-project.properties.png" />
  <small>sonar-project.properties</small>
</center>

5. Acesse o servidor do SonarQube, acesse o seu perfil, vá em "My Account", em seguida "Security" e gere um token que será utilizado para enviar os dados de análise do seu projeto para o SonarQube.

<center style="margin: 20px">
  <img style="margin-top: 20px" src="{{ site.baseurl }}/content/2016-12-05-sonarqube-code-analysis/generate-token.png" />
  <small>sonar-project.properties</small>
</center>

6. No prompt  de comandos, execute a seguinte linha:

`c:\meuprojeto\fontes>sonar-scanner -Dsonar.login=14273dca3f9c735f924610aa3ae1503a0ffe3816 -Dsonar.projectVersion=2.0.0.1

<center style="margin: 20px">
  <img style="margin-top: 20px" src="{{ site.baseurl }}/content/2016-12-05-sonarqube-code-analysis/command-prompt.png" />
  <small>sonar-project.properties</small>
</center> 

7. Verifique os logs gerados. De acordo com as configurações de qualidade do módulo QualityGate do SonarQube o processo de análise pode retornar um erro. Os erros podem ser vistos no portal do Sonar acessando o projeto.

Veja também:
- [Integração Contínua .NET + GitLab + GitLab CI](http://cwisoftware.github.io/drops/integra%C3%A7%C3%A3o-cont%C3%ADnua-com-gitlab-e-dotnet)
- [Analyzing with SonarQube Scanner for MSBuild](http://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+MSBuild)

---
layout: post
date: 2016-06-09T12:00:00-02:00
title: "A integração contínua na maturidade de projetos CWI"
author: brunotrassante
abstract: >
  Um processo de integração contínua é fundamental para um projeto de alta maturidade técnica. Entenda para o que devemos olhar. 
---

Apesar de não ser um tema com vida própria no nosso modelo de maturidade, o processo de integração contínua está intimamente ligado a ele. 
Tendo participação nos tópico: **Geração de Pacotes**, **Testes**, **Performance**, **Segurança**, **Código** e **Banco de Dados**, praticamente não se pode falar em maturidade sem IC.

Separei abaixo todos os conceitos que são citados no nosso modelo de maturidade junto com dicas de como implementá-los:


## Geração de Pacotes

# Integração de fontes a cada commit

A vantágem básica da integração contínua. Garantir que a base de código que está sendo alterada por várias pessoas ao mesmo tempo continua funcionando (ou pelo menos compilando) quando juntam-se as peças novamente.
Esse item resume-se basicamente em ter alguma servidor de IC configurado para olhar para o seu projeto.

Na CWI, não possuímos uma solução única de integração contínua corporativa, principalmente por trabalharmos com tantas tecnologias e stacks diferentes, porém, temos exemplos de configuração e alguns servers prontos para atender a todos os gostos:

*Corporativas:* 

- [Git CWI - CI](http://ci-gitlab.cwi.com.br/) _(exemplos: Arezzo, Unimed Volta Redonda)_
    - A integração contínua nativa do nosso gitlab interno. Todos os projeto hospedados no nosso git já têm uma pré configuração no CI. Funciona com simples configurações em formato yml.
    - Veja mais no [post do Alexandre Machado](http://cwisoftware.github.io/drops/integra%C3%A7%C3%A3o-cont%C3%ADnua-com-gitlab-e-dotnet) sobre o tema
- [TFS Build](https://www.visualstudio.com/en-us/docs/build/overview) _(exemplos: Núcleo de Tecnologia)_
    - Versão atual da CWI (2013) é bastante simples de usar para projetos hospedados no TFS, porém recomendado para builds simples. Passos mais elaborados dão bem mais trabalho do que em outras soluções. A versão 2015 promete ser em um patamar muito melhor, inclusive superando as "concorrentes". 

*Locais* (criados e mantidos pelos projetos, em servidores próprios)

- [Jenkins](https://jenkins.io/) _(exemplos:Renner, Gocil, Coca-cola, Núcleo de Tecnologia, N outros)_ 
    - Solução opensource, com muitos plugins desenvolvidos pela comunidade. É a opção mais popular na CWI.
- [Team City](https://www.jetbrains.com/teamcity/) _(exemplos: L'Oréal, Walmart, CNova, Equinix, etc)_
     - Bastante similar ao Jenkins, oferece uma interface mais simples, e possui uma configuração facilitada (estilo next next). Possui muitas facilidades de integração, em especial para .NET. Sua versão Free permite atender apenas 20 projetos por servidor.

*Soluções de Nuvem*

Projetos que estejam em repositórios públicos podem se aproveitar de soluções na nuvem grátis. Com o revés de não serem execuções prioritárias, concorrendo com projetos de toda a comunidade.

-  [Travis](https://travis-ci.org) 
    _(ex na CWI: Projeto Enlighten (Núcleo de Tecnologia) )_
    - O mais famoso dos servidores de IC na núvem. Não muito amistoso para execuções de integração para windows.     
- [AppVeyor](https://www.appveyor.com)
    _(ex na CWI: Projeto accounts (Núcleo de Tecnologia) )_ 
    - Como descrito na própria página incial do site: "travisIC of the windows world". Mesma proposta. 
    
    
# Deploys Automatizados 

O final de um build bem sucedido, o último passo é enviar o código integrado, validado e testado para algums servidor para testar sua instalação. 

O processo pode ser totalmente automático, quando a confiança na integração contínua já é total, mas enquanto não se chega lá, o mais comum é deixar tudo pronto para, com uma interação humana muito simples, os fontes sejam publicados no ambiente desejado. Em geral, usa-se alguma ferramenta visual específica para o deploy para monitorar qual versão está em cada ambiente e controlar quem tem permissões para fazer tal publicação. 
Um bom exemplo de ferramenta para esse fim utilizado na CWI é o *Octopus Deploy*. Para mais detalhes, leia o [post do Giovani Barili](http://cwisoftware.github.io/drops/integracao-continua-e-deploy-continuo-tempo-e-dinheiro) que aborda esse tema específico. 

# Deploys de Baixo Risco 

Talvez um dos maiores trunfos pouco explorados pelas empresas e equipes de desenvolvimento é a utilização de estratégias de deploy de baixo risco, como o [Blue Green Deployment](http://martinfowler.com/bliki/BlueGreenDeployment.html), sugerido por Martin Fowler em 2010 ou o [Canary Release](http://martinfowler.com/bliki/CanaryRelease.html), bastante utilizado por grandes players como Netflix, Amazon, Google e Facebook.

Em todos os casos, as técnicas sempre consistem em fazer publicações em ambientes diferentes dos atualmente em produção e em seguida redirecionar as conexções para esse novo ambiente. Técnicas desse tipo permitem rollbacks praticamente imediatos de publicações e enterra por completo o medo de implementar um Continuous Deployment. 


## Testes

# Executados a cada build na IC 

Que uma suite bem estruturada de testes automatizados unitários e funcionais são a alma de um software de alta qualidade e longevidade. já não se discute mais. Porém essa prática não sobrevive muito tempo quando não casada a IC.
A medida que são adicionados mais e mais testes ao projeto sua execução começa a tomar algum tempo e seria insustentável manter a execução de todos eles manualmente para cada nova mexida no código. 

Assim, essas duas técnicas ganham força uma com a outra: 

- Permite que o desenvolvedor siga trabalhando, sabendo que suas alterações não estragam funcionalidades já construídas, sem pagar o preço de esperar a execução completa dos testes manualmente.
- Da, cada vez mais, confiabilidade ao processo de deploy contínuo, tendo a certeza de "alguém" retestou todo o software antes de publicá-lo.  


## Performance

# Testes de performance e carga 

Seguindo a mesma lógica das funcionalidades, a performance precisa também ser garantida via testes que rodem via IC.
É bastante comum que um relatório ou funcionalidade tenha uma boa performance quando foi criado, mas ao longo do tempo, outras alterações nas classes e banco degradem esses números. Manualmente, é muito improvável que alguém vá voltar a todas as telas para medir esses tempos daquilo que já funcionava bem. Testes que meçam os tempos de respostas e garantam que sejam sempre menor do que um valor determinado, é uma simples solução.

Outro importante passo a se considerar é o quanto as novas mudanças no sistema estão melhorando ou piorando o número de usuários e requisições que ele aguenta.
Grandes players de sistemas online costumam ter um passo exlusivo da sua IC apenas para testes de carga, antes de liberar a nova versão para produção.
Quando já se tem testes de performance criados para controlar os tempos de resposta das requisições, organizar o step da bateria de teste de carga é apenas uma questão de estratégia de quando rodar e qual hardware/ambientes utilizar.

## Segurança

# Testes de segurança 

Novamente, o dia a dia do desenvolvimento de um software pode deixar passar falhas até para os mais experientes engenheiros. Se essa falha é de segurança, como uma nova chamada de serviço colocada no javascript sem token que apenas a aplicação deveria conhecer ou uma query string com parâmetro ID que não confere se o que está sendo pedido pertence mesmo ao usuário, sabemos que é muito pior.
Uma ótima forma de dormir tranquilo a noite é saber que sua integração contínua executa diversos testes de segurança com uma boa frequência no seu sistema.

Um exemplo simples dessa abordagem é a execução do programa [OWASP ZAP como um dos passos da IC](https://www.securify.nl/blog/SFY20150303/automating_security_tests_using_owasp_zap_and_jenkins.html). Construido pelos próprios engenheiros da OWASP, executa dezenas de milhares de testes exploratórios de segurança apenas sabendo a URL do sistema.  


## Código

# Validações de estilo, complexidade e boas práticas 

A primeira vez na vida você submente o seu código a um validador automático de boas práticas o seu ego sai abalado... mas o aprendizado que se tira dali e o reflexo deste no seu código são um divisor de águas na vida de todo desenvolvedor.
Não temos uma restrição exata no nosso modelo sobre o que medir, o que temos como macro grupos são: 

- _Complexidade dos Métodos:_ Geralmente medindo através da complexidade ciclomátia ou alguma formula envolvendo esta e o número de linhas do método.
- _Estilo:_ Pequenas melhorias na escrita do código mas que facilitam muito a leitura, como não deixar espaços em branco desnecessários, variáveis com bons nomes, nada de código comentado, etc.
- _Boas Práticas específicas da linguagem:_ Muito variável, mas as principais ferramentas de validação do mercado já possuem baterias específicas para as linguagens mais populares. 

Um outro fator que reforça ainda mais este ponto: Cada dia mais, até nossos clientes tem se apoiado em ferramentas de validação automatizada do mercado para validar as entregas feitas pela CWI. Não as usarmos por nossa própria conta já não pode ser algo aceitável.

As principais ferramentas que usamos hoja na CWI são:

- [SonarQube](http://www.sonarqube.org/)
    - Talvez a mais popular das ferramentas de mercado, possuí cobertura para todas as linguagens que usamos na CWI. É facilmente integrável com seu plugin específico do Jenkins, além de ter extenções para uso direto no VisualStudio e Eclipse.
-  [Nitriq](http://www.nitriq.com)
    - Infelizmente descontinuado a alguns anos, mas ainda muito útil. Ferramenta em .NET que permite escrever de forma bem simples suas próprias regras de validação de código.
- [CodeClimate](https://codeclimate.com)
    - Bastante usado pela sua integração direta com o GitHub e poder ser usado como SaaS. Posui engines para Ruby, Python, PHP, entre outros.

- E muitos outos: 
    - JSLint
    - JS-Hint
    - FXCop
    - StyleCop
    - MS Code Analysis
    - Etc


## Banco de Dados

# Atualizar a base de dados na publicação 

Não é incomum termos uma integração contínua bastante robusta, cheia de validações e testes, porém, depois da publicação no novo ambiente, o último passo é alguém abrir manualmente a base de dados e rodar os scripts de atualização...
É fato: onde há ação manual humana, há chance muito maior de falhas, ainda mais em atividades rotineiras e repetitivas.
Salvo por restrições diretas impostas por clientes que querem "correr esses riscos" e deixar sua base de dados acessível apenas para seus DBAs, não ter os scripts de atualização de banco sendo rodados pela IC é certamente uma má prática bastante perigosa.

Algumas ferramentas facilitam muito esse trabalho, como:

- [Liquibase](http://www.liquibase.org/)
    - Ferramenta de versionamento de scripts de banco. Bastante rustica, mas muito popular. Os scripts de atualização e rollback seguem sendo escritos pelos desenvolvedores/DBAs, porém é possível configurar quais arquivos são de qual versão, quais devem ser sempre executados, etc, através de um arquivo XML .
- [Flayway](https://flywaydb.org/)
    - Solução mais simples e menos versátil do que o Liquibase. Trabalha apenas com renomeações de arquivos sql.  
 
 
## Uhuuu! Ta, mas... por onde eu começo?

Não existe uma forma correta de escolher a ordem dos steps da sua integração contínua. Para cada caso um item pode trazer mais valor que outro, e algumas peculiaridade e restrições de clientes podem até inviabilizar alguns passos.
Os steps básicos de uma integração costumam ser: 

Build => Testes Unitários => Validações de Código => Deploy em ambiente de Testes => *O céu e o limite a partir daqui!*       

 Na primeira encarada, parece ser um desafio grande, mas depois que se começa, passa a ser quase um hobby. Colocar um novo step na IC e colher os frutos vira realmente prazeroso.
 Você tem algum outro passo na sua IC que merece destaque? Compartilhe conosco!
 A nossa maturidade de projetos está sempre em evolução, e quem molda os caminhos da CWI é cada um de nós.      

---
layout: post
date: 2016-06-28T14:20:31-03:00
title: "Automatizando Ambientes com Ansible"
author: johnvoloski
abstract: >
  Integração Contínua e Deploy Contínuo pedem Ambientes Automatizados
---
Uma necessidade que cada vez mais encontramos ao desenvolver um projeto é a automatização de tarefas, sejam elas de natureza do próprio projeto bem como de ambientes.

A nível de desenvolvimento podemos tomar como exemplo os próprios testes automatizados, um projeto em rails por exemplo, fundamentalmente teremos testes unitários e testes comportamentais.

Para executarmos esses testes automatizados, certamente precisaremos de algumas dependências dos mesmos, como bibliotecas do sistema operacional, ou até mesmo outros softwares.

Precisamos também disponibilizar ambientes variados, um ambiente de desenvolvimento, um ambiente de QA, um ambiente para homologação, bem como um ambiente de produção.

Somente nesse passo inicial, teríamos 4 ambientes de visualização do produto, mais 2 ambientes para execução de testes automatizados que promovem o build em nossa integração contínua.

Se tivéssemos o trabalho de instalar cada um destes ambientes teríamos que fazer a instalação de 6 ambientes exatamente iguais, dependendo da complexidade das dependências, estaríamos suscetíveis a diversos problemas.

E é neste momento que adotar uma ferramenta de gerenciamento de configurações faz a diferença.

Dentre as "mais famosas" no mercado temos o [Puppet](https://puppet.com/), o [Chef](https://www.chef.io/), o [Salt](https://saltstack.com/) e o [Ansible](https://ansible.com/).

Aqui vamos abordar a automatização de ambientes com a ferramenta Ansible.

![image](https://avatars3.githubusercontent.com/u/1507452?v=3&s=100)

# Ansible

Dentre todas as ferramentas que já degustei, o ansible se mostrou a mais simples, a com menor curva de aprendizado. As configurações no ansible são simples descrições dos requisitos dos ambientes em texto em arquivos .yml.

Diferente da maioria das ferramentas o ansible não traz aquele modelo "agente -> master", ele necessita apenas das credênciais de acesso de uma máquina, não existe preocupações de monitoramento de agentes, se está ativo, atualizado, desatualizado, etc...

Para começarmos a trabalhar com ansible, é interessante entender o que significa cada taxonomia:

[Glossário](http://docs.ansible.com/ansible/glossary.html)

# Instalação

Convenções: para simplificarmos, utilizaremos um **Ubuntu 16.04**.

Antes de começarmos a instalar efetivamente o ansible, vamos instalar as suas dependências.

```
sudo apt-get install python-pip python-setuptools python-paramiko python-yaml python-jinja2 python-httplib2 python-six
```

Geralmente as versões disponibilizadas em pacotes para o sistema operacional, estão desatualizadas quanto a repositório principal, então iremos instalar a partir do github do projeto.

```
git clone https://github.com/ansible/ansible.git ~/ansible/ --recursive
cd ~/ansible/
```

Quando precisarmos atualizar a versão do ansible, basta executar:

```
cd ~/ansible/
git pull --rebase
git submodule update --init --recursive
```

Logo após instalarmos, devemos adicionar o setup do ansible no "~/.bashrc":

```
echo "source ~/ansible/hacking/env-setup -q" >> ~/.bashrc
```

Verificando a versão instalada:

```
ansible --version
```

Feito! Concluímos a nossa instalação.

# Como funciona?

Nesta etapa vamos criar um pequeno projeto, a fim de entendermos como funciona, vamos assumir que estaremos configurando um servidor **nginx**.

Vamos criar a estrutura de pastas do nosso projeto:

*raiz*

```
mkdir ~/www_ansible/
```

*diretório onde estão as nossas roles, no caso para instalar o nginx*

```
mkdir -p ~/www_ansible/roles/
```

*diretório onde estão neste exemplo, nosso manipulador de serviços do sistema operacional*

```
mkdir -p ~/www_ansible/roles/nginx/handlers
```

*diretório onde estão as tarefas que serão executadas na role nginx*

```
mkdir -p ~/www_ansible/roles/nginx/tasks
```

*diretório onde estão os templates que podemos aplicar em algum diretório do sistema operacional*

```
mkdir -p ~/www_ansible/roles/nginx/templates
```

O primeiro passo para começarmos a criar nossa **playbook** é criamos nosso inventário de máquinas.
Existem diversas configurações possíveis, para mais detalhes:

[Documentação Oficial](http://docs.ansible.com/ansible/intro_inventory.html)

```
touch ~/www_ansible/hosts
```

Logo após criarmos o arquivo, vamos cadastrar nossa máquina local como o host candidato a **playbook**, adicionando o conteúdo ao arquivo:

```
[www]
127.0.0.1 ansible_user=username ansible_ssh_pass=password ansible_become=yes
```

Uma **playbook** no ansible é basicamente uma associação entre quais *roles* serão aplicadas em cada *host*:

*playbook padrão, arquivo padrão que o ansible lê*

```
touch ~/www_ansible/site.yml
```

*playbook nginx, arquivo com mapeamentos customizados* 

```
touch ~/www_ansible/nginx.yml
```

No playbook padrão vamos incluir o playbook nginx, adicionando o seguinte conteúdo ao arquivo:

```
- include: nginx.yml
```

No playbook nginx vamos definir para localhost a role nginx, adicionando o seguinte conteúdo ao arquivo:

```
- name: nginx playbook
  hosts: www
  roles:
    - nginx
```

O arquivo padrão identificado dentro das pastas da estrutura da role é o **main.yml**, então vamos criar o arquivo dentro de cada pasta da nossa role:

```
touch ~/www_ansible/roles/nginx/handlers/main.yml
touch ~/www_ansible/roles/nginx/tasks/main.yml
touch ~/www_ansible/roles/nginx/templates/main.yml
```

Agora vamos criar nossa *task*, instalar o pacote do nginx, adicionando o seguinte conteúdo ao arquivo **~/www_ansible/roles/nginx/tasks/main.yml**:

```
- name: install nginx
  apt: name=nginx state=present
```

Agora vamos criar nosso *handler* para o role nginx, a idéia aqui é construirmos facilitadores para os pacotes instalados na role, vejamos o exemplo de *reinicialização* do serviço, adicionando o seguinte conteúdo ao arquivo **~/www_ansible/roles/nginx/handlers/main.yml**:

```
- name: restart nginx
  service: name=nginx state=restarted enabled=yes
```

Primeiro vamos criar o template padrão para o nginx:

```
touch ~/www_ansible/roles/nginx/templates/default.conf
```

Agora vamos aplicá-lo ao pacote instalado através de uma *task*, adicionando o seguinte conteúdo ao arquivo **~/www_ansible/roles/nginx/tasks/main.yml**:

```
- name: install nginx template
  template: src=default.conf dest=/etc/nginx/conf.d/default.conf
```

Porém sabemos, que ao aplicar um template as configurações só serão absorvidas após o serviço ser reinicializado, então vamos adicionar este complemento a tarefa de instalação do template, adicionando o seguinte conteúdo ao arquivo **~/www_ansible/roles/nginx/tasks/main.yml**:

```
  notify: restart nginx
```

A estrutura da tarefa de instalação do template do nginx deverá ficar desta maneira:

```
- name: install nginx template
  template: src=default.conf dest=/etc/nginx/conf.d/default.conf
  notify: restart nginx
```

Na anotação **notify** estamos nos referindo ao nome do **handler** que criamos, o mesmo irá reinicializar o serviço do nginx, aplicando o template.

E agora podemos executar o nosso playbook, aplicando as mudanças ao nosso host:

```
  ansible-playbook -i hosts site.yml
```

Os fontes deste post, estão [neste repositório](https://github.com/johnvoloski/nginx-with-ansible) no GitHub.

# Conclusão

As ferramentas de automatização de ambientes vem melhorando ao longo dos anos, estão ficando cada vez mais simples.
E isso é importante para que a adoção seja grande.

O ansible possui muitas outras opções para a construção das receitas, por exemplo, assign de variáveis para templates, ou arquivos de task, handlers, e etc...

O tempo "perdido" na automatização de ambientes, é uma parte fundamental na qualidade da entrega, atualmente para todos os projetos que participo, tenho receitas desde ambiente de desenvolvimento com **vagrant** até o ambiente de produção.

Para se aprofundar mais no ansible, indico a [documentação oficial](http://docs.ansible.com/ansible/index.html), lá você vai conseguir aprender tudo que é possível fazer com a ferramenta de uma forma atualizada.

Até a Próxima! E que o **ansible** esteja com vocês :)

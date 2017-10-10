Instalação
----------

Existem 2 formas de instalar o ambiente de desenvolvimento:

1. usando seu ambiente local
2. usando Vagrant 
3. usando docker

## Instalando no seu ambiente local (Linux e OS X)

### Dependências

- Ruby 2.2.3 (preferencialmente instalado com [rbenv](https://github.com/rbenv/rbenv))
- Bundler (`gem install bundler`)

### Instalação

```
git clone https://github.com/CWISoftware/drops.git && cd drops
bundle install
```

### Rodando o projeto

```
jekyll s
```

Acesse [http://localhost:4000/drops/](http://localhost:4000/drops/)

## Instalando no seu ambiente local Windows

### Dependências (X86 ou x64)

- [Ruby 2.2.3 (MSI Installer x86)](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.2.3.exe)
- [Ruby 2.2.3 (MSI Installer x64)](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.2.3-x64.exe)
- [Ruby DevKit 2 (x86)](http://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe)
- [Ruby DevKit 2 (x64)](http://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe)

### Instalação

Executar MSI Installer marcando a opção de adicionar variável de ambiente.
Extrair DevKit na pasta raiz onde foi instalado o Ruby. (Ex.: C:\Ruby e C:\DevKit)
Via CMD, navegar até a pasta de instalação do DevKit:

```
ruby dk.rb init
ruby dk.rb install
```

Instale o bundler:

    gem install bundler

*Caso tenha erro de certificado no comando anterior adicione a seguinte fonte no rubygems `gem sources -a http://rubygems.org`*

Clonando o projeto e instalando dependências:

```
git clone https://github.com/CWISoftware/drops.git && cd drops
bundle install
```

*Caso tenha erro de certificado ao rodar `bundle install` modifique o arquivo `Gemfile` para utilizar a source HTTP ao invés de HTTPS (ou seja, apenas remova o "S", salve o arquivo e rode o comando novamente).*

### Rodando o projeto

    jekyll s

## Instalando com Vagrant

### Dependências

- Vagrant
- VirtualBox

### Instalação

```
git clone https://github.com/CWISoftware/drops.git && cd drops
vagrant up
```

### Rodando o projeto

```
vagrant ssh
cd /vagrant && ./scripts/run_guest.sh
```
Acesse [http://localhost:4000/drops/](http://localhost:4000/drops/)

## Instalando com Docker

### Dependências

- [docker compose](https://docs.docker.com/compose/install/#install-compose)

### Instalação

```
git clone https://github.com/CWISoftware/drops.git && cd drops
docker-compose up
```
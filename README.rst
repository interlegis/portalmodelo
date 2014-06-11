**********************************************************
Como montar o ambiente de desenvolvimento do Portal Modelo
**********************************************************

.. contents:: Conteúdo
   :depth: 2

Este documento detalha o processo de desenvolvimento do Portal Modelo.

Definições
==========

Buildout
  É uma ferramenta open source de construção de software. Ela foi
  desenvolvida utilizando-se a linguagem de programação Python e fornece
  suporte à criação de instâncias de aplicações, principalmente àquelas
  escritas em Python, fornecendo ferramentas para criação de aplicações
  através de peças, denominadas parts, que podem ser módulos Python,
  servidores web, servidores de aplicação e etc.

Git
  É um sistema de controle de versão distribuído e um sistema de
  gerenciamento de código fonte, com ênfase em velocidade. O Git foi
  inicialmente projetado e desenvolvido por Linus Torvalds para o
  desenvolvimento do kernel Linux, mas foi adotado por muitos outros
  projetos.

GitHub
  É um Serviço de Web Hosting Compartilhado para projetos que usam o
  controle de versionamento Git. É escrito em Ruby on Rails pelos
  desenvolvedores da Logical Awesome. O GitHub possui planos comerciais e
  gratuitos para projetos de código aberto.

mr.developer
  É uma extensão do zc.buildout que faz simples trabalhar com buildouts que
  contem muitos pacotes dos quais você precisa desenvolver algum.

Pré-requisitos do ambiente de trabalho
======================================

Para trabalhar no desenvolvimento do Portal Modelo você vai precisar o
seguinte:

* Uma conta no GitHub
* Git instalado na sua máquina
* Python 2.7 instalado
* Dependências de desenvolvimento do seu sistema operacional

Abrindo uma conta no GitHub
---------------------------

Para abrir uma conta no GitHub só precisa visitar o endereço
https://github.com/ e assinar. O GitHub não tem custo para usuários
individuais que não armazenam nele repositórios privados.

Instalação do Git (Ubuntu/Debian)
---------------------------------

Para instalar o Git numa máquina rodando Ubuntu/Debian só precisa executar o
seguinte comando::

    sudo apt-get install git

Instalação de dependências para desenvolvimento (Ubuntu/Debian)
---------------------------------------------------------------

Para as dependências para desenvolvimento numa máquina rodando Ubuntu/Debian
só precisa executar o seguinte comando::

    sudo apt-get install build-essential python-setuptools python-virtualenv zlib1g-dev libxslt1-dev libxml2-dev

Instalação de dependências para ``plone.app.ldap``
--------------------------------------------------

O plone.app.ldap depende da biblioteca python-ldap e precisam ser instaladas algumas outras dependências no servidor onde o Portal Modelo será instalado. Se o Servidor roda o sistema operacional GNU/Linux Debian ou Ubuntu, poderá ser utilizando o seguinte comando para instalá-las::

    sudo apt-get install python-dev libldap2-dev libsasl2-dev libssl-dev

Descarregando o código fonte do Portal Modelo
=============================================

O Buildout de desenvolvimento do Portal Modelo está armazenado no GitHub no
endereço::

    https://github.com/interlegis/interlegis-portalmodelo

Para começar trabalhar precisamos clonar este repositório::

    git clone git@github.com:interlegis/interlegis-portalmodelo.git

Agora, dentro da pasta interlegis-portalmodelo temos uma copia do Buildout do
Portal Modelo::

    cd interlegis-portalmodelo

O pacote contém uma configuração de desenvolvimento que vai nos ajudar no
processo. Vamos usar ela por padrão criando um link simbólico com o nome
buildout.cfg apontando à configuração de desenvolvimento::

    ln -s development.cfg buildout.cfg

Agora podemos fazer bootstrap da configuração::

    python bootstrap.py

O script bootstrap.py é o encarregado de inicializar o processo, ele vai
descarregar os pacotes necessários para poder executar a configuração, vai
criar várias pastas e vai gerar o script bin/buildout.

O script bin/buildout faz várias coisas no contexto do Portal Modelo:

* descarregar o Plone e as dependências do projeto
* criar o script de inicialização do Plone
* criar o script de testes
* inicialização dum site padrão do Portal Modelo
* instalação de helper scripts para várias outras tarefas

Todas essas funcionalidades estão declaradas no arquivo development.cfg e em
vários outros estendidos por ele; alguns desses arquivos são listados na
sequência:

buildout.d/base.cfg
  É a configuração base, contém todos os parâmetros que são comuns tanto
  para a configuração de desenvolvimento, quanto para a configuração de
  produção.

buildout.d/sources.cfg
  Contém os endereços dos repositórios dos pacotes que conformam o Portal
  Modelo. Estas fontes são usadas pela extensão mr.developer para
  descarregar o código fonte dos pacotes quando precisamos solucionar algum
  problema numa dependência.

buildout.d/versions.cfg
  Contém uma lista das dependências e suas versões utilizadas no contexto do
  Portal Modelo. Este arquivo só inclui dependências não listadas no core do
  Plone, ou versões de dependências que foi necessário modificar para
  corregir algum problema ou adicionar alguma funcionalidade.

checkouts.cfg
  Contém uma listas de pacotes em modo desenvolvimento; normalmente esta
  lista se encontra vazia, mas no processo de correção de problemas ou
  desenvolvimento de novas funcionalidades irá listar o nome do pacotes que
  estamos utilizando em modo desenvolvimento.

Ao rodar o script bin/buildout ele vai pegar todas as dependências listadas
nos arquivos de configuração e vai criar um Plone site com a configuração
padrão do Portal Modelo.

Exemplos de desenvolvimento
===========================

Para ilustrar o processo de desenvolvimento no Portal Modelo vamos utilizar
dos exemplos; no primeiro vamos fazer modificações para consertar um problema
fictício num pacote. No segundo, vamos criar um novo pacote e incluir ele no
Portal Modelo.

Correção de problemas ou adição de funcionalidades em pacotes existentes
------------------------------------------------------------------------

Neste exemplo vamos consertar um bug fictício no pacote
interlegis.portalmodelo.ombudsman.

A primeira coisa que precisamos fazer é editar o arquivo checkouts.cfg e
adicionar o nome do pacote que vai ficar em modo desenvolvimento::

    auto-checkout =
        interlegis.portalmodelo.ombudsman

Agora, ao rodar o script bin/buildout, o mr.developer vai trazer o código
fonte do pacote e vai colocar na pasta src. Podemos consertar o bug e rodar os
testes para verificar que todo deu certo::

    bin/test

Quando a mudança tem sido aceita precisamos fazer um novo release do pacote
que estava em desenvolvimento.

Editamos o arquivo buildout.d/versions.cfg e alteramos o número da nova
versão. Não devemos esquecer remover a mudança feita no arquivo checkouts.cfg
para evitar que o pacote continue em modo desenvolvimento.

Ao rodar novamente o script bin/buildout vamos conferir que a nova versão é
descarregada e que nosso Portal Modelo foi atualizado com sucesso.

Adição de novos pacote ao Portal Modelo
---------------------------------------

O procedimento é muito parecido com o anterior. Primeiro vamos precisar do
endereço do repositório novo pacote.

Com essa informação vamos editar o arquivo buildout.d/sources.cfg para
adicionar o novo endereço::

    [sources]
    interlegis.portalmodelo.novo = git git@github.com:intelegis/interlegis.portalmodelo.novo.git

Isso indica que o pacote interlegis.portalmodelo.novo está armazenado no
GitHub e vamos utilizar o protocolo Git para pegar o código fonte ele.

Depois editamos  o arquivo checkouts.cfg e adicionamos o nome do novo pacote::

    auto-checkout =
        interlegis.portalmodelo.novo

Ao rodar o script bin/buildout o código do novo pacote e incluso na pasta src.

O procedimento após um release é o mesmo definido acima, sem esquecer de
adicionar uma entrada no arquivo buildout.d/versions.cfg para o novo pacote.

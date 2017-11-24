# Tutorial Rails- Do Zero ao Deploy

## Sobre o Rails

Ruby on Rails, ou simplesmente Rails, é um framework web escrito na linguagem Ruby. Alguns sites que usam Rails incluem GitHub, Basecamp, Airbnb e Scribd.

Referências em português:

- <http://www.maujor.com/railsgirlsguide/>
- <https://www.caelum.com.br/apostila-ruby-on-rails/> 


Neste tutorial iremos criar e colocar no ar um projeto Rails.

### Ferramentas que serão utilizadas
- Could9(c9)
- Github
- Heroku
- Code Climate

 A seguir, instruções para o ambiente de programação online, o [Cloud9](https://c9.io/).

## Configurando o ambiente Cloud9

Vamos usar um ambiente de programação totalmente online, o [Cloud9](https://c9.io/).
**Para utilização desse ambiente é necessário o usuário possuir um cartão de crédito para validação. Saliento que não é nenhum valor será cobrado posteriormente pela utilização da plataforma.**

### Primeiramente, crie um ambiente (workspace)

- Entre em <https://c9.io/>
- Crie uma conta (ou entre com sua conta do GitHub se possuir)
- Clique em "Create a new workspace"
	- Em workspace name, digite `sistemaweb`.
	- Escolha o template "Rails tutorial"

Esse ambiente pode ser usado para mais de um projeto caso necessário.

### Configurando o editor

Em Ruby, a convenção é usar 2 espaços para indentar seu código. Para configurar o editor do Cloud9, siga os seguintes passos:

- Clique no menu Cloud9 > Preferences.
- Em Project Settings > Code Editor (Ace), marque a opção Soft Tabs e mude o número para 2.

### Conhecendo o editor

O Cloud9 possui 3 painéis:

- esquerda: navegador de arquivos
- baixo: terminal do Linux
- centro: editor de código-fonte

## Criando um projeto rails

Clique no terminal(painel abaixo) e digite o seguinte:

    rails new meuapp
    cd meuapp

(Se o comando `rails` não funcionar, instale-o com o comando `gem install rails`.)

A primeira linha executa o comando `rails`, que automatiza diversas tarefas durante o desenvolvimento de uma aplicação Rails. Em especial, o comando `rails new` cria uma aplicação Rails em um novo diretório com o nome fornecido.

O comando `cd` é um comando do Linux, abreviação para *change directory*, que muda o **diretório atual**. Alguns outros comandos úteis:

- `pwd`: imprime o caminho do diretório atual (o comando é uma abreviação para *print working directory*)
- `ls`: lista os arquivos do diretório atual
- `cd ..`: muda o diretório atual para o diretório pai do diretório atual
- `cd abc`: muda o diretório atual para o diretório chamado `abc` dentro do diretório atual

Note que o diretório `meuapp` agora aparece no navegador de arquivos (painel à esquerda).

### Rodando a aplicação web

Clique no terminal e certifique-se que o diretório atual é o diretório `meuapp` que você criou (você pode usar o comando `pwd` pra isso).

Se estiver usando o Cloud9, digite o seguinte comando:

    rails server -b $IP -p $PORT

Esse comando inicia um servidor web para sua aplicação que pode ser acessível pelo IP `$IP` e porta `$PORT`. 

**Cloud9**. Para acessar a aplicação, clique no botão "Preview", ao lado do menu. Isso vai abrir uma aba de um navegador no endereço `https://sistemaweb-USER.c9users.io/`, onde `USER` é seu nome de usuário no Cloud9. Você pode acessar esse site de qualquer navegador conectado à Internet.

**Parando a aplicação**. Para fechar o servidor web, clique no terminal onde o servidor está rodando e pressione Ctrl+C. Se você tentar acessar sua aplicação, ela não estará mais rodando.

**Dica de uso do terminal**. Você pode usar a seta para cima para visualizar os últimos comandos executados. Além disso, a tecla Tab pode ser usada para completar algumas palavras digitadas parcialmente (nome de comando, nome de arquivo etc.) usadas em comandos.


**Servidor web de desenvolvimento**. O Cloud9 permite que seu servidor web possa ser acessado a partir de qualquer máquina conectada à Internet através do endereço `https://sistemaweb-USER.c9users.io/`, mas esse endereço não deve ser acessado pelos usuários finais da aplicação:

- O servidor não é robusto o suficiente para suportar muitos acessos simultâneos
- O Cloud9 coloca o seu ambiente de desenvolvimento (incluindo o servidor Rails) para dormir depois de um tempo sem atividade, o que efetivamente torna a aplicação inacessível.

### Criando uma página

Vamos agora usar o recurso de geração de código do Rails para criar a funcionalidade de gerenciar postagens em um blog. Toda postagem (ou post) possui título e conteúdo.

Acesse o terminal do ambiente e pare o servidor se estiver rodando. Então digite os seguintes comandos:

    rails generate scaffold post titulo:string conteudo:text
    rake db:migrate
    rails server -b $IP -p $PORT

A seguir, edite o arquivo `config/routes.rb`, inserindo a seguinte linha antes da linha `end`:

    root to: redirect('/posts')

Então inicie o servidor e acesse novamente a página (`https://sistemaweb-USER.c9users.io/`). Note que você pode listar, criar, visualizar, editar e remover postagens.

## Configurando alguns serviços

### GitHub

Execute os seguintes comandos no terminal:

    git init
    git add .
    git commit -m "Versao inicial."

Acesse o [GitHub](https://github.com/) e crie um repositório.

    git remote add origin https://...
    git push -u origin master

Referências em português:

- [Conceitos de controle de versão](https://www.youtube.com/watch?v=ILUvjs8xvSs)
- <http://tableless.com.br/tudo-que-voce-queria-saber-sobre-git-e-github-mas-tinha-vergonha-de-perguntar/> -- ver também links no final
- <http://rogerdudler.github.io/git-guide/index.pt_BR.html>
- <https://githowto.com/pt-BR>
- Livro: <https://git-scm.com/book/pt-br/v1>

### Heroku

Heroku é um provedor de nuvem do tipo plataforma como serviço (platform as a service, PaaS) que suporta diversas linguagens de programação. Fundada em 2007, originalmente dava suporte somente a Ruby.

**Adaptando a aplicação**. A nossa aplicação está originalmente configurada para usar o banco de dados SQLite, mas esse banco de dados não está disponível no Heroku; no lugar dele, vamos usar o banco de dados PostgreSQL. Para isso, altere o arquivo Gemfile para ficar da seguinte forma:

```ruby
group :development, :test do
  gem 'sqlite3'
  # ...
end
group :production do
  gem 'pg'
end
```

A seguir, rode o seguinte comando para atualizar as dependências

```bash
bundle install --without production
```

A seguir, faça um *commit* das suas modificações:

    git add .
    git commit -m "Configura banco de dados PostgreSQL em produção."

Isto é, mova a linha `gem :sqlite3` para o grupo `:development, :test` e crie um grupo `:production` com `gem 'pg'` (referente ao PostgreSQL). O Heroku é o ambiente de produção.

**Enviando para o Heroku**. Primeiramente, crie uma conta no [Heroku](https://www.heroku.com/).

O template "Rails tutorial" já vem com o [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) instalado, que é uma ferramenta de linha de comando para gerenciar aplicações no Heroku.

Acesse o terminal do seu ambiente de desenvolvimento e digite:

    heroku login

(Se o comando `heroku` não funcionar, instale-o com o comando `gem install heroku`.)

Digite suas credenciais. A seguir, digite:

    heroku create meuapp

Sua aplicação vai rodar em <https://meuapp.herokuapp.com/>. Note que o nome `meuapp` deve ser único; escolha um nome que nunca foi usado ou deixe em branco para receber um nome aleatório.

O comando `heroku create` cria um espaço para sua aplicação no Heroku, cria o banco de dados PostgreSQL e configura um novo *remote* no Git que será usado para enviar o código de sua aplicação para o Heroku. Você pode entrar no [Heroku](https://heroku.com/) e ver a lista de apps ativos no serviço. Note que existem diversas [limitações no plano gratuito](https://devcenter.heroku.com/articles/limits).

Para enviar a aplicação para o Heroku:

    git push heroku master

Agora tente acessar a sua aplicação em <https://meuapp.herokuapp.com/> (troque `meuapp` pelo nome de sua aplicação no Heroku). Deu erro? Vamos ver os logs para entender o problema:

    heroku logs

Você também pode usar `heroku logs --tail` para ver as mensagens de log em tempo real (use Ctrl+C para encerrar). Você deve encontrar um erro parecido com o seguinte:

> PG::UndefinedTable: ERROR:  relation "posts" does not exist

O problema é que o banco de dados do Heroku não foi inicializado. Para inicializá-lo, execute:

    heroku run rake db:migrate

O comando `heroku run` executa no Heroku o comando passado como parâmetro.


### Code Climate

[Code Climate](https://codeclimate.com/) é um serviço de avaliação automática de qualidade de código-fonte através de análise estática. Ele aponta problemas e dá uma nota para seu código entre 0.0 e 4.0.

Para usar o Code Climate, basta entrar no serviço usando a conta do GitHub. A partir daí, você pode adicionar seu repositório no GitHub ao Code Climate para que ele realize a análise.

**Colocando badges do Code Climate no GitHub**. Abra o projeto no Code Climate, clique em "Settings" e então "Badges". Copie e cole os trechos de código (escolha o formato Markdown) no seu arquivo `README.md`.

Referências:

- <https://tasafo.org/2014/10/06/entrega-continua-com-ruby-on-rails-github-code-climate-travis-ci-e-heroku/>
- <http://labs.bluesoft.com.br/do-zero-ao-deploy/>

Outras referências:

- <http://www.maujor.com/railsgirlsguide/heroku.php>
- Tutorial de Rails do professor Rodrigo Rocha para a disciplina MATA56

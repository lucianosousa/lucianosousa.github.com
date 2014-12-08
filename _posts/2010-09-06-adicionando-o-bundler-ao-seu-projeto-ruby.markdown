---
layout: post
title: Adicionando o Bundler ao seu projeto Ruby
tags:
- bundler ruby rubygems gem gems rails soudev development
status: publish
type: post
published: true
meta:
  _edit_last: '7005667'
  _wp_old_slug: ''
  jabber_published: '1283744061'
---

O <a href="http://gembundler.com/" target="_blank">Bundler</a> é um gerenciador de gems de um projeto em ruby. O uso dele tem crescido  bastante ultimamente e ele também foi integrado as dependências do <a href="http://rubyonrails.org/" target="_blank">Rails 3</a>.

Se você desenvolve um mesmo projeto em mais de um computador, as funcionalidades dele o ajudarão muito.

Ao baixar/clonar seu projeto para algum computador diferente do normal, você terá de baixar <strong>todas</strong> as <strong>gems</strong>, uma por uma, para então começar a dar continuidade ao <strong>desenvolvimento</strong>. Caso possua o <strong>bundler</strong>, você só precisará de <strong>um</strong> comando para ele instalar todas as gems. Isso também o poupará muito tempo.

Para instalá-lo, <strong>digite</strong> no seu terminal/prompt:

```
bash-3.1$ gem install bundler
```

Feito isso, acesse o root path do seu projeto e crie um arquivo com o nome <strong>Gemfile</strong>

```
bash-3.1$ touch Gemfile
```

Feito isso, você acaba de criar o arquivo que o <a href="http://gembundler.com/" target="_blank">bundler</a> necessita para instalar todas as gems que seu projeto utiliza. Para habilitá-lo você precisa adicionar antes de mais nada, o repositório de onde serão baixadas as <strong>gems</strong>. Adicione a seguinte linha ao seu arquivo <strong>Gemfile</strong>:

```
source "http://rubygems.org"
```

Finalizando, você só precisa adicionar as <strong>gems</strong> do seu projeto ao <strong>Gemfile</strong> da seguinte maneira:

```
gem "primeira gem"
gem "segunda-gem"
```

Feito isso, em todo e qualquer lugar que você precisar instalar suas gems, utilize o comando:

```
bash-3.1$ bundle install
```

Pronto. Seus problemas com <strong>dependências</strong> acabaram.
Qualquer <strong>dúvida</strong> ou <strong>sugestão</strong> sinta-se a vontade em <strong>comentar</strong>.
Abraços e até o <strong>próximo</strong> post.

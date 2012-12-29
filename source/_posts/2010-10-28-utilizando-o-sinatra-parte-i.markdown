---
layout: post
title: Utilizando o Sinatra, parte I
tags: []
status: publish
type: post
published: true
meta:
  jabber_published: '1288280539'
  _wp_old_slug: ''
  _edit_last: '7005667'
---

Que <a href="http://ruby-lang.org/">Ruby</a> é uma linguagem muito fácil de se utiilzar você já deve saber.

Que <a href="http://www.rubyonrails.org/" target="_blank">Rails</a> é um framework muito simples de se utilizar você também deve saber.

Mas e o <a href="http://www.sinatrarb.com/">Sinatra</a>? Você já conhece?

Sinatra é um micro-framework para criar aplicações web de uma forma simples, fácil e rápida.
A vantagem dele sobre qualquer outro framework web é que você consegue resultados rápidos em um esforço mínimo, enquanto que com outros frameworks você consegue um resultado mais robusto com um tempo relativamente maior.

O uso do <a href="http://www.sinatrarb.com/">Sinatra</a> é recomendado para subir pequenas aplicações rapidamente, mas, grandes projetos também podem ser feitos no Sinatra.

O Sinatra possui a seguinte estrutura:
<ul>
	<li>a pasta raiz do projeto;</li>
	<li>um arquivo .rb qualquer que é inicializado no console;</li>
	<li>pasta views;</li>
	<li>pasta public;</li>
	<li>arquivos de classe quaisquer;</li>
</ul>
Exemplificando:

Antes de mais nada, vamos instalar a gem do sinatra:

{% codeblock %}
$ gem install sinatra
{% endcodeblock %}

Agora, vamos criar uma pasta para guardar os arquivos desse projeto, a minha se chamará <em>sinatra-example</em>.
Dentro dessa pasta, vamos criar o arquivo <em>example.rb</em> que terá todas as rotas de requisição.

Obrigatoriamente, devemos fazer o <em>require</em> do sinatra no <em>example.rb</em>.
As primeiras linhas do arquivo serão:

{% codeblock %}
require 'rubygems'
require 'sinatra'
{% endcodeblock %}

Com isso, podemos utilizar o sinatra em nosso projeto. Nosso primeiro exempo o famoso 'hello world' será exibir uma mensagem qualquer no browser.

No example.rb, digite:

{% codeblock %}
get '/hello' do
&nbsp;&nbsp;'hello world'
end
{% endcodeblock %}

Com isso, você pode ver o seguinte: o funcionamento do sinatra é baseado em requisições HTTP, como o get. O parâmetro '/hello' é a url digitada no browser e o que está entre aspas dentro do <code>do</code>, <code>end</code> é o que será renderizado no browser quando a url digitada pelo usuário for a '/hello'.
No console, digite:

{% codeblock %}
$ ruby example.rb
{% endcodeblock %}

Você receberá uma mensagem como:

{% codeblock %}
[2010-10-22 13:41:23] INFO  WEBrick::HTTPServer#start: pid=1788 port=4567
{% endcodeblock %}

Isso diz que o sinatra foi inicializado na porta 4567.

Abra no browser <code>http://localhost:4567/hello</code> e veja o resultado.

Ok, isso é muito simples. Vamos ao próximo passo. Iremos mostrar uma página html criada por nós.
Por padrão, o sinatra lê todos os arquivos html dentro da pasta <code>app/views</code>, logo, vamos criar essa pasta.
Pasta criada, crie um arquivo chamado index.erb e coloque seu código html dentro dele.
Nota, o sinatra lê diversos tipos de templates, html, erb, haml, erubis, etc. Vamos utilizar o .erb.
Criado seu arquivo index.erb, abra o arquivo example.rb e crie a requisição para ele.


{% codeblock %}
get '/' do
&nbsp;&nbsp;erb :index
end
{% endcodeblock %}

Após qualquer alteração no arquivo example.rb, devemos  reinicializar o sinatra.
Após reinicializar a aplicação, abra o endereço <code>http://localhost:4567/</code>

Todo o código digitado na sua index.erb será renderizado no navegador.

Vamos para o último exemplo desse post:

No sinatra, qualquer variável declarada no escopo da requisição, pode ser utilizada na view do projeto.

Vamos exemplifica isso criando uma requisição onde o usuário irá inserir um nome qualquer na url '/user/nome' e ele será renderizado na view.

Nossa requisição ficará assim:


{% codeblock %}
get '/user/:name' do
&nbsp;&nbsp;@name = "You entered #{params[:name]}"
&nbsp;&nbsp;erb :user
end
{% endcodeblock %}

Dentro da pasta views, vamos criar uma página user.erb para exibir a variável '@name'.

Você pode fazer isso da forma que lhe convir, no meu exemplo ficará assim:

{% codeblock %}
&lt;center&gt;
&lt;h1&gt;&lt;%= @name %&gt;&lt;/h1&gt;
&lt;/center&gt;
{% endcodeblock %}

Por hora é isso, nos próximos posts pretendo exemplificar o uso de filtros.

Há um exemplo do meu código no <a href="http://github.com/lucianosousa/sinatra-example">github</a>

Abraços.

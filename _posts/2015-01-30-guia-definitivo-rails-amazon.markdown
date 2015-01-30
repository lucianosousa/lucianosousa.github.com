---
layout: post
title: O Guia Definitivo do deploy Rails App no Amazon EC2 em 15 passos
status: published
type: post
published: true
---

Este guia serve não só como um lembrete próprio de como instanciar um servidor mas também como uma forma de ajudar as pessoas a instanciarem suas próprias aplicações Rails em uma VPS.

A distribuição Linux utilizada aqui é a Amazon Linux AMI, baseada no centOS, ou seja, a maioria ou todos os passos aqui podem ser replicados também para essa distribuição.

Let's go!

Partindo do pressuposto que você já temos acesso SSH:


Passo 1) Atualizar Servidor

```
$ sudo yum update
```

Passo 2) Pré requisitos do ruby e da sua aplicação

```
$ sudo yum install gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison glibc git ImageMagick memcached tcl libcurl-devel
```


Passo 3) Instalar banco de dados, no meu caso, Postgresql:

```
$ sudo yum install postgresql93-server postgresql93-devel postgresql93-libs postgresql93-contrib
```

ou

```
$ sudo yum install mysql-server mysql-devel
```


Passo 4) Instalar <a href="https://rvm.io" target="_blank">RVM</a>/<a href="https://www.ruby-lang.org/en/">Ruby</a>

```
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3

$ \curl -sSL https://get.rvm.io | bash -s stable

$ rvm install 2.2.0
```

Habilitar global cache no RVM para quando atualizar versão do ruby não instalar as mesmas gems novamente

```
$ rvm gemset globalcache enable

$ rvm use 2.2.0@global --default

$ gem install rack --no-ri --no-rdoc
```


Passo 5) Instalar <a href="http://nodejs.org" target="_blank">Node.js</a> (libv8)

```
$ wget http://nodejs.org/dist/v0.10.36/node-v0.10.36.tar.gz

$ tar zxvf node-v0.10.36.tar.gz

$ cd node-v0.10.36/

$ ./configure && make

$ sudo make install clean
```


Passo 6) Alterar configuração do Kernel do Linux para prevenir erro de memória no Redis(vide: http://redis.io/topics/faq)

```
$ sudo vim /etc/sysctl.conf
```

Adicionar a seguinte linha:

```
vm.overcommit_memory = 1
```


Passo 7) Instalar <a href="http://redis.io" target="_blank">Redis</a> para serviço de fila (<a href="http://sidekiq.org/" target="_blank">sidekiq</a>)

```
$ wget http://download.redis.io/releases/redis-2.8.19.tar.gz

$ tar zxvf redis-2.8.19.tar.gz

$ cd redis-2.8.19/

$ make
```

Conferir se correu tudo bem no make

```
$ make test
```

Setar o Redis como um serviço

```
$ sudo mkdir /etc/redis /var/lib/redis

$ sudo cp src/redis-server src/redis-cli /usr/local/bin
```

Copiar redis.conf com configurações específicas de daemon (conferir <a href="https://gist.github.com/lucianosousa/71a248c900b5773d7ab4" target="_blank">aqui</a> se é a última revisão)

```
$ sudo wget -O /etc/redis/redis.conf https://gist.githubusercontent.com/lucianosousa/71a248c900b5773d7ab4/raw/5068052a6dd20473f63857824f38e8d58866d332/redis.conf
```

Copiar daemon file do redis (conferir <a href="https://gist.github.com/lucianosousa/d53f902f16d284111afd" target="_blank">aqui</a> se é a última revisão)

```
$ sudo wget -O /etc/init.d/redis-server https://gist.githubusercontent.com/lucianosousa/d53f902f16d284111afd/raw/cba6b0eb350e2ee80503acddae1f646b921482c8/redis-server
```

Configurações para tornar um serviço no centOS

```
$ sudo chmod 755 /etc/init.d/redis-server

$ sudo chkconfig --add redis-server

$ sudo chkconfig --level 345 redis-server on

$ sudo service redis-server start
```


Passo 8) Configurar SSH para deploy no servidor novo

Editar ~/.ssh/config

```
Host *meudominio.com
  ForwardAgent yes
```

```
$ ssh-add ~/.ssh/id_rsa
```


Passo 9) Setar configuração de deploy

```
#config/deploy.rb

lock '3.3.5'

set :application, 'my-app'
set :repo_url, 'git@github.com:repo/app.git'

set :deploy_to, "/home/ec2-user/my-app"

set :linked_files, fetch(:linked_files, []).push('config/database.yml')
set :linked_dirs, fetch(:linked_dirs, []).push('bin', 'log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')

set :keep_releases, 3

desc "Check if agent forwarding is working"
task :forwarding do
  on roles(:all) do |h|
    if test("env | grep SSH_AUTH_SOCK")
      info "Agent forwarding is up to #{h}"
    else
      error "Agent forwarding is NOT up to #{h}"
    end
  end
end

namespace :deploy do
  desc "Restart application"
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      execute :touch, release_path.join("tmp/restart.txt")
    end
  end

  after :finishing, "deploy:restart"
end
```

```
#config/deploy/env.rb
role :app, %w{ip-or-domain}
role :web, %w{ip-or-domain}
role :db,  %w{ip-or-domain}

server 'ip-or-domain', user: 'ec2-user', roles: %w{web app}, my_property: :my_value

```


Passo 10) Testar configuração de deploy

```
$ bundle exec cap *ENV* deploy:check
```


Passo 11) Configurar banco de dados

Postgresql

```
$ sudo service postgresql93 initdb

$ sudo service postgresql93 start

$ sudo su - postgres

$ psql

# create user xyz with password 'xyz';

# create database banco-minha-app owner xyz;

# \q
```


Alterar método de login do postgresql para permitir login do novo usuário:

```
$ sudo vim /var/lib/pgsql93/data/pg_hba.conf
```


```
local   all             all                                     md5
host    all             all             127.0.0.1/32            md5
```

Reiniciar postgresql

```
$ sudo service postgresql93 restart
```

Mysql

```
$ sudo service mysqld start

$ /usr/bin/mysqladmin -u root password 'A-New-Password-Here'

$ mysql -u root -p

$ create user 'new-user'@'localhost' identified by 'A-New-Password-Here';
```


Passo 12) Instalar <a href="https://www.phusionpassenger.com/" target="_blank">Passenger</a>/<a href="http://nginx.org" target="_blank">Nginx</a>

```
$ wget http://s3.amazonaws.com/phusion-passenger/releases/passenger-4.0.58.tar.gz

$ tar zxvf passenger-4.0.58.tar.gz

$ sudo mkdir /opt/passenger/

$ sudo mv passenger-4.0.58/ /opt/passenger/

$ cd /opt/passenger/passenger-4.0.58/

$ rvmsudo ./bin/passenger-install-nginx-module
```

Aceitar instalação do nginx pelo passenger

Configurar nginx.conf com user específico e outras coisas

```
user ec2-user
worker_processes 5

http {
  ...
  passenger_nodejs /usr/local/bin/node;
  passenger_default_user ec2-user;
}
```

Nginx como serviço

Conferir último daemon <a href="https://gist.github.com/lucianosousa/e7ffa626b63a23d71161" target="_blank">aqui</a>

```
$ sudo wget -O /etc/init.d/nginx https://gist.githubusercontent.com/lucianosousa/e7ffa626b63a23d71161/raw/56982a31b8fc68e96db119ed6a402fa3e993a74f/nginx

$ sudo chmod 755 /etc/init.d/nginx

$ sudo chkconfig --add nginx

$ sudo chkconfig --level 345 nginx on

$ sudo service nginx start
```


Passo 13) Abrir configuração do Security group da amazon para aceitar requisição na porta 80


Passo 14) Start no memcached

```
$ sudo service memcached start
```

Passo 15) Deploy

```
$ bundle exec cap ENV deploy
```


TODO: Cron para todos os serviços. Cenas de um próximo post


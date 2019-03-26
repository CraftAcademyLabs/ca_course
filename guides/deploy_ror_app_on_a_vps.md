---

title: "DevOps"
subtitle: "Deploy Ruby on Rails on Ubuntu 18.04"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "DevOps, Deployment, DigitalOcean, Rails"
keywords: [DevOps, Deployment, DigitalOcean, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Deploy Ruby on Rails on Ubuntu 18.04

So far in this camp, you've been deploying most of your Rails application on Heroku. This required little to zero configurations on your part. All you needed to do was create an account with them, get your project ready and push your code to a git repository provided by Heroku. You didn't have to worry about setting up a server and installing all the necessary software and package to host a Rails application.

In this guide, we will be setting up a Ruby on Rails production environment on a Virtual Private Server (VPS) running Ubuntu 18.04 LTS. We will cover all the steps required to create, provision the server and getting it ready to host our application; We will also prepare our Rails Application to be deployed on that server.

We are going to setup our server on [Digital Ocean](https://do.co). They provide a cloud platform that allows you to easily manage your server infrastructure.

![Digital Ocean](https://github.com/CraftAcademy/ca_course/raw/master/guides/images/do.png)

## Create a Virtual Private Server

Once you've create your account on Digital Ocean you'll get access to your dashboard from where you will manage most of your resources.

![Select droplet OS](https://github.com/CraftAcademy/ca_course/raw/master/guides/images/create-droplet-1.png)

Select your droplet size

![Select droplet size](https://github.com/CraftAcademy/ca_course/raw/master/guides/images/select-droplet-size.png)

Select a region closer to you

![Select region](https://github.com/CraftAcademy/ca_course/raw/master/guides/images/select-region.png)

Give your droplet a name and upload your ssh-keys to be able to login to the server later on

![SSH Key and Droplet name](https://github.com/CraftAcademy/ca_course/raw/master/guides/images/ssh-key-droplet-name.png)

## Create `deploy` user

We first need to ssh into our server as the `root` user, so grab the ip number of the server.

```shell
$ ssh root@your-vps-ip-address
```

Then we can create the user

```sh
$ adduser deploy  // enter a password when asked and store it somewhere safe
$ adduser deploy sudo
$ echo "deploy  ALL=(ALL:ALL)   NOPASSWD: ALL" > /etc/sudoers.d/deploy
```

The last line of the above command is just so we don't have to type the password for the `deploy` user everytime we try to execute a command using `sudo`

Switch to the `deploy`

```sh
$ su - deploy
```

Then do the following to copy the authorized keys from the `root` user into the ssh config for `deploy`. When this is done, you should be able to ssh directly into the server as `deploy` and won't need to make use of `root`

```sh
$ mkdir .ssh
$ sudo cat /root/.ssh/authorized_keys > .ssh/authorized_keys
```

Now log out and try to log into the server as a the newly created `deploy` user before you carry on with the next steps

```sh
$ ssh deploy@your-vps-ip-address
```

Install system and security updates on the server

```sh
$ sudo apt update && sudo apt upgrade -y  // If you get a popup select the option to keep local settings
```

## Security - Setup a firewall

When setting up a server to host a web app, it's important to setup some basic security rules. Using `ufw`, a program used to manage firewall rules on Ubuntu, we're going to setup some rules on what ports or service we want to grant/deny public access to

Executing the following commands will block all incoming requests to the server and allow all outgoing requests from the server.

```sh
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
```

Next we enable `ssh` access if we don't do this, we will not be able to log into the server moving forward.

```sh
$ sudo ufw allow ssh
```

And because we're deploying a web application, we should enable public access to port `80` (`http`) and port `443` (`https`).

```sh
$ sudo ufw allow http
$ sudo ufw allow https
```

After setting all those rules, we need to enable `ufw`

```sh
$ sudo ufw enable
```

You can execute the following command to make sure everything has been configured correctly

```sh
$ sudo ufw status
```

## Installing Ruby

Given that we'll be deploying a rails application, we should install Ruby on our system. To do that we'll use `rbenv` a ruby version manager. Let's go ahead and set that up.

First let's install some dev dependencies:

```sh
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt update
sudo apt install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev nodejs yarn -y
```

Installing `rbenv`

```sh
$ cd
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ exec $SHELL

$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ exec $SHELL
```

Install the ruby version you need for your application. For this demo we'll be using 2.5.1

```sh
$ rbenv install 2.5.1
$ rbenv global 2.5.1
$ ruby -v
```

If you get the error `rbenv: no such command 'install'`, then run `sudo apt-get install rbenv ruby-build` and try again.

Next we need to install Bundler

```sh
$ gem install bundler
$ rbenv rehash
```

And finally we install rails

```sh
$ gem install rails

```

## Installing the Nginx Web Server

We will use Passenger + Nginx on our Production server because it's fairly easy to setup.

```sh
$ sudo apt install -y dirmngr gnupg
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
$ sudo apt install -y apt-transport-https ca-certificates

$ sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list'
$ sudo apt update

$ sudo apt-get install -y nginx-extras passenger

$ sudo ls /etc/nginx/passenger.conf
```

Now that Nginx has been installed and we've configured passenger, we need to restart Nginx by using the following command:

```sh
sudo systemctl start nginx.service
```

To verify that our Passenger installation is configured properly, we need to run the following command:

```sh
sudo /usr/bin/passenger-config validate-install
```

Next, we should update the Nginx configuration to point Passenger to the version of Ruby we installed earlier on.

```sh
sudo vim /etc/nginx/passenger.conf
```

Locate the line that sets `passenger_ruby` and modify it to point to the ruby executable

```sh
passenger_ruby /home/deploy/.rbenv/shims/ruby;
```

Once we change that, we should restart Nginx with the new configuration

```sh
sudo systemctl restart nginx.service
```

## PostgreSQL Database Setup

Install postgres

```sh
$ sudo apt install postgresql postgresql-contrib libpq-dev -y
```

Switch to postgres user and create a `deploy` postgres user, we will need to specify a password, make it a strong one, store it somewhere since we will need it in our rails application to manage our database. When that is done we exit the `postgres` user

```sh
$ sudo su - postgres
$ createuser -s -r -P deploy
$ exit
```

As the `deploy` user we need to create the database for our application

```sh
$ createdb -O deploy my_app_name_production
```

## Capistrano Setup

```ruby
group :development do
  gem 'capistrano'
  gem 'capistrano-bundler', '~> 1.2'
  gem 'capistrano-rails', '~> 1.3'
  gem 'capistrano-env-config'
  gem 'capistrano-rbenv', '~> 2.1', '>= 2.1.1'
  gem 'capistrano3-puma', '~> 3.1', '>= 3.1.1'
  gem 'capistrano-passenger'
  gem 'capistrano-yarn'
end
```

Run `bundle install`

```sh
$ cap install STAGES=production
```

Modify the `Capfile`

```ruby
# Load DSL and set up stages
require 'capistrano/setup'

# Include default deployment tasks
require 'capistrano/deploy'

# Load the SCM plugin appropriate to your project:
require 'capistrano/scm/git'
install_plugin Capistrano::SCM::Git

require 'capistrano/rbenv'
require 'capistrano/bundler'
require 'capistrano/rails/assets'
require 'capistrano/rails/migrations'
require 'capistrano/passenger'
require 'capistrano-yarn'

# Load custom tasks from `lib/capistrano/tasks` if you have any defined
Dir.glob('lib/capistrano/tasks/*.rake').each { |r| import r }
```

Update Deploy configuration `deploy.rb`

```ruby
# config valid for current version and patch releases of Capistrano
lock "~> 3.11.0"

# config/deploy.rb
set :rbenv_type, :user # or :system, depends on your rbenv setup
set :rbenv_ruby, '2.5.1'

set :application, 'appname'
set :repo_url, 'git@github.com:organisation/appname.git'

set :branch, 'master'

set :deploy_to, '/var/www/appname'

# Default value for :linked_files is []
set :linked_files, %w[
  config/database.yml
  config/master.key
]

# Default value for linked_dirs is []
set :linked_dirs, %w[
  log tmp/pids tmp/cache tmp/sockets vendor/bundle
  public/system public/uploads public/.well-known
]

# Default value for default_env is {}
# set :default_env, { path: "/opt/ruby/bin:$PATH" }

# Default value for keep_releases is 5
set :keep_releases, 5

namespace :deploy do
  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      execute :touch, release_path.join('tmp/restart.txt')
    end
  end

  after :publishing, 'deploy:restart'
  after :finishing, 'deploy:cleanup'
end
```

Replace the content of `config/deploy/production.rb` with

```ruby
server 'your-server-ip-addres', user: 'deploy', roles: %w[ web db app ]
set :ssh_options, forward_agent: true
```

To deploy your app you'll need to execute the following command.

```sh
$ bundle exec cap production deploy
```

The first time you execute the deploy command, you'll get a couple failure because some files or folders haven't been created yet on the server.

You need to ssh into the server and create the folder you've specified on the `deploy_to` variable in `config/deploy.rb`. You will also need to create inside of that folder the following files and add the relevant configs in them:

```sh
/var/www/appname/shared/config/database.yml
/var/www/appname/shared/config/master.key
```

To the database.yml add

```ruby
production:
  <<: *default
  database: db_name_production
  username: deploy
  password: <%= Rails.application.credentials.dig :postgres, :password, :production %>
```

and update your rails credentials file

```sh
$ rails credentials:edit
```

```ruby
postgres:
  password:
    production: db_password
```

## Configure Nginx host

Last but not least, we need to tell Nginx how to route requests to our application. To do so we need to setup a host. Open the file `/etc/nginx/site-availables/default` and replace its content with:

```conf
server {
  listen 80;
  listen [::]:80 ipv6only=on;

  server_name yourdomainame.com;

  passenger_enabled on;
  rails_env    production;
  root         /var/www/appname/current/public;

  # redirect server error pages to the static page /50x.html
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
      root   html;
  }
}
```

Save the file and restart `nginx`

```sh
$ sudo systemctl restart nginx
```

## Wrap up

This should do it. At this stage you have set up your very own Virtual Private Server.

**So I have a brand new DigitalOcean droplet with Ubuntu 14.04.1 LTS. Yay! Now I'm facing the seemingly monumental task of setting up the enviroment so I can deploy my Rails app. Given that DevOps is not my strong side, I find this a little intimidating. But, what the hell, how hard can it be?**

### Getting started... ###

I recon that the first thing I need is an web server, right? So I log in to the server as root (`ssh root@172.00.00.00` <- the 172.00... being YOUR droplet's IP address) and decide to go with Apache that I install by running:

```
apt-get install apache2
```

That also creates the `www-data` group for me.

Okay, that gives me a vanilla apache2 installation and if I call my ip address in my browser I get the default Apache page. So far so good.

### Moving on... ###

My next step is to set to run the formidable 'Rails Ready' script originally written by Josh Frye (https://github.com/craftacademy/railsready). This will install most of the libraries needed to run Rails, Bundler, Phusion Passenger, Rails gems and Git for me. Since I'm used to RVM (Ruby Version Manager) I'll choose that option when presented with a choice during the installation process. 


```
wget --no-check-certificate https://raw.github.com/joshfng/railsready/master/railsready.sh && bash railsready.sh
``` 

Following the instructions on screen, I "logout and back in to access Ruby" and move on to install my version of ruby that I need for my application by running:

```
rvm install 2.2.0
```

and set RVM to use that ruby by default

```
rvm use --default 2.2.0
``` 

Apache cannot run Ruby like it can PHP, Perl, or Python because there's no built-in Ruby module that works as well as those do. So I need an application server. Phusion Passenger (informally also known as mod_rails and mod_rack among the Ruby community) is a free web server and application server with support for Ruby, Python and Node.js. Passenger incorporates a number of essential features in a very convenient package that works very well and doesn't demand a lot of attention. Out of the box it's pretty much ready to go, witch is cool as convention over configuration is what I preffer. ;-) You can read more about Passenger on http://www.phusion.nl. 

I go ahead and install the passenger gem...

```
gem install passenger
```

...and move on to install the Passenger Apache module:

```
passenger-install-apache2-module
```

The installer asks you what languages you are interested in and lets you choose. I chose `ruby`. Right after that a check for necessary software is performed and you are presented with information on how to install missing components, if there are any need for that. In my case there was...

[screen dump]

I also had to increase my virtual memory:

```
sudo dd if=/dev/zero of=/swap bs=1M count=1024
sudo mkswap /swap
sudo swapon /swap
```
  
After all necessary installations I rerun the installer... 

```
passenger-install-apache2-module
```

...that will compile and install the Apache 2 module. (This can tak a while. Be patient)

The installer tells me to update the Apache configuration (```nano /etc/apache2/apache2.conf```) file with following settings: 

```
LoadModule passenger_module /usr/local/rvm/gems/ruby-2.2.0/gems/passenger-4.0.59/buildout/apache2/mod_passenger.so
   <IfModule mod_passenger.c>
     PassengerRoot /usr/local/rvm/gems/ruby-2.2.0/gems/passenger-4.0.59
     PassengerDefaultRuby /usr/local/rvm/gems/ruby-2.2.0/wrappers/ruby
   </IfModule>
```
   
It also gives me an example for how to set up a virtrual host to deploying a web application. (I'll be saving that part for later...) 

``` 
   <VirtualHost *:80>
      ServerName www.yourhost.com
      # !!! Be sure to point DocumentRoot to 'public'!
      DocumentRoot /somewhere/public    
      <Directory /somewhere/public>
         # This relaxes Apache security settings.
         AllowOverride all
         # MultiViews must be turned off.
         Options -MultiViews
         # Uncomment this if you're on Apache >= 2.4:
         #Require all granted
      </Directory>
   </VirtualHost>
```

### The Database... ###
Alright, moving on to setting up the database. I use Postgresql in my application so I start with installing that on my server:

```
apt-get install postgresql postgresql-contrib
```

I don't have to do much in order to configure the database on this server as I will be using the default user `postgres` so this pretty much covers it all.

Okay, so now I need to get the code for my app and set up:

I cd into the `/var/www/` folder...

```
cd /var/www/
```

...and use `git clone` to get the application source (There are far more better ways to get the code deployed to the server and I'll be setting that up as well but this method will work for now).

```
git clone https://github.com/tochman/repository_name
```

Now, we need to logout from the server and login back as any other user but `root` (See note below for creating a new user and setting upp permissions).

```
sudo su username
```

So, I start with creating a gemset for my application

```
rvm gemset create my_app

rvm use 2.2.0@my_app
```

Now it is time to install all dependencies

```
bundle install --without development test
```

And If you encounter some permission problems, make sure to set the folders where the gems are being installed. I had to run:

```
sudo chown -R username:www-data /usr/local/rvm/gems
sudo chmod -R g+s /usr/local/rvm/gems
```

Any way, all is good on my side! I'll be moving on to setting up my database (locally)...

Hmm, when running `rake db:create` I get an error:

```
FATAL:  Peer authentication failed for user "postgres"
```

Yeah, so lets set a password for user `postgres` 

```
sudo -u postgres psql
\password
Enter password: ...
```

and make sure to edit `pg_pg_hba.conf`

```
sudo nano /etc/postgresql/9.3/main/pg_hba.conf
```

and set the authentication method for user `postgres` to `trust` 

```
# Database administrative login by UNIX sockets
local   all         postgres                          trust
```

This should allow me to both create, migrate and seed the database.

```
RAILS_ENV=production bundle exec rake db:create
RAILS_ENV=production bundle exec rake db:migrate
RAILS_ENV=production bundle exec rake db:seed
```

And it does! All migrations set up and ready to go. So let's compile the assets...

```
RAILS_ENV=production bundle exec rake assets:clobber
RAILS_ENV=production bundle exec rake assets:precompile
```

Also, I'm using 'ActiveStorage' in my application so I need to install ImageMagick to be able to generate previews. Here's the command I need to run:

```
sudo apt-get install imagemagick
```

### Finishing up... ###
Okay, time to set up the virtual host so we can get this show on the road! ...or, rather 'this app on the internet!'

```
sudo nano /etc/apache2/sites-available/my_app.conf
```

Add this bare minimum set of settings:

```   
   <VirtualHost *:80>
      DocumentRoot /var/www/my_app/public
      RailsEnv production
      ErrorLog /var/www/my_app/log/error.log
      CustomLog /var/www/my_app/log/access.log combined
      <Directory  /var/www/my_app/public>
         # This relaxes Apache security settings.
         AllowOverride all
         # MultiViews must be turned off.
         Options -MultiViews
         # Uncomment this if you're on Apache >= 2.4:
         Require all granted
      </Directory>
   </VirtualHost>
```
   
Save the file and enable the site with:

```
sudo a2ensite my_app
```

```
service apache2 reload
```

Okay, wanna give it a try? 

### Final words... ###

**All this could in many way be done in many different ways and this workflow could be improved. The important thisng, at the end of the day, is that you practice and are not afraid to get your hands a bit dirty doing DevOps!**


### Side notes ###

New user:

```
sudo adduser username
``` 

Add to www-data group

```
sudo usermod -a -G www-data username
```

Add to sudo group

```
sudo usermod -a -G sudo username
```

Change folder permissions

```
sudo chown -R username:www-data /var/www/my_app
sudo chmod -R g+s /var/www/my_app
``` 


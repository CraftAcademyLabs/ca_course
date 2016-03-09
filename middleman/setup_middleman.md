## Setup Middleman

If you don't have Ruby installed yet, please refer to our setup instructions for [Linux](../terminal/linux.md) or [Mac OS X](../terminal/mac_osx.md)

Once you're done, head to your terminal and run the following command:

```shell
$ gem install middleman
```

This will install Middleman, its dependencies and command line tools for using Middleman.

### Create a new project - `middleman init`

Let's build a middleman skeleton project. From your terminal, navigate to the folder where you would like to create your project and run the following command:

```shell
$ middleman init my_site
```

This will create a folder named `my_fancy_site` in the current directory.

### Serving the Application - `middleman server`

Middleman ships with a local server to test your application during development. 

```shell
$ cd my_site
$ middleman server
```

This will start up the server and you should now be able to access the site at [http://localhost:4567/](http://localhost:4567/)


### Directory Structure

```shell
my_site/
+-- .gitignore
+-- Gemfile
+-- Gemfile.lock
+-- config.rb
+-- source
    +-- images
    ¦   +-- background.png
    ¦   +-- middleman.png
    +-- index.html.erb
    +-- javascripts
    ¦   +-- all.js
    +-- layouts
    ¦   +-- layout.erb
    +-- stylesheets
        +-- all.css
        +-- normalize.css
```

***Add Description of main folders here***
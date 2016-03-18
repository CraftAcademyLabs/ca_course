## Setup Middleman

If you don't have Ruby installed yet, please refer to our setup instructions for [Linux](https://craftacademy.gitbooks.io/caa_precourse/content/terminal/linux.html) or [Mac OS X](https://craftacademy.gitbooks.io/caa_precourse/content/terminal/mac_osx.html)

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

This will create a folder named `my_site` in the current directory and generate starter files for your project.

You'll be prompted the following questions to which you need to answer with either (`y`)es or (`n`)o:

```shell
Do you want to use Compass? n
Do you want to use LiveReload? y
Do you want a Rack-compatible config.ru file? n
```

### Serving the Application - `middleman server`

Middleman ships with a local server to test your application during development. 

```shell
$ cd my_site
$ middleman server
```

This will start up the server and you should now be able to access the site at [http://localhost:4567/](http://localhost:4567/)

![Middleman welcome screen](welcome_to_middleman.png)

Great! We have successfully setup middleman and created our project. Now let's go through the files that have been generated for us.

### Directory Structure

A tipical middleman application will have a directory structure that looks like this:

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

#### The `source` directory

The `source` folder is where you'll spend most of your time while building your website. By default it contains folders for JavaScript, CSS and images, but you can re-organise them at your own convenience. We'll see how to achieve that later in this chapter.

#### The `config.rb`

The `config.rb` file is were you will find the settings for your Middleman application. It also contains commented documentation on how to enable complex features such as compile-time compression, blog-mode, etc.

#### The `Gemfile`

Just like any other ruby project you have worked with so far, Middleman makes use of Bundler `Gemfile` to manage your gem dependencies.

#### Other Middleman directories

Middleman makes use of extra directories for specific purposes. Each of these folders are children of your main application folder.

##### The `build` directory

This is where your static website files will be compiled and exported to. 

##### The `lib` directory

The `lib` directory will house your external Ruby modules. These modules will contain helpers to use while building your application.

##### The `data` directory

Data files allow you to create `.yml`, `.yaml` or `.json` files in the `data` folder and makes this information available in your templates. We will be covering Data files in another section of this chapter.

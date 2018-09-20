---
title: "Bower"
subtitle: "Week 2"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Bower, JQuery"
keywords: [Bower, JQuery]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## Bower
Bower is a front-end package manager built by Twitter. As a package manager, Bower simplifies installing and updating project dependencies, which are libraries you use in your project. 

Browsing all the library websites, downloading and unpacking the archives, copying files into the project folder — all of this can be replaced with a few Bower commands in the terminal.

![](bower_landing.png)

### Installing Bower

Bower can be installed using npm, the Node package manager (If you don't already have npm installed, head over to the Node.js [website](http://nodejs.org) and follow the install instructions). The npm program is included in Node.js.

Once you have npm installed, open up your terminal and run the following command:

```
$ npm install -g bower
```
This will install Bower globally on your system.

Now that you have Bower installed, we can start looking at the commands that are used to manage packages.

### Finding Packages

There are two different ways that you can find Bower packages. Either using the online component directory, or using the command line utility.

To search for packages on the command line you use the search command. This should be followed by your search query.

```
$ bower search [query]
```
For example to search for packages that contain the word `jquery` you could do the following:

```
$ bower search jquery
```
This command would return a whole bunch of results, some of which are displayed in the snippet below.
```
Search results:

    jQuery https://github.com/jquery/jquery.git
    jquery https://github.com/jquery/jquery-dist.git
    jquery.x https://github.com/jljLabs/jquery.x.git
    jquery.Q https://github.com/jsbuzz/jQuery_Q.git
    jt_jquery https://github.com/vicanso/jt_jquery.git
    jquery-m https://github.com/meetup/jquery.git
    jquery.j2d https://github.com/fsggs/jquery.j2d.git
    jquery.ua https://github.com/cloudcome/jquery.ua.git
    jquery-ol https://github.com/jordanmarkov/jquery-ol.git
    jquery-tm https://github.com/trymore/jquery-tm.git
    jquery.ui https://github.com/jquery/jquery-ui.git
    hn.jquery https://github.com/loi-chuanoi/jquery.git
    ...
```
Each result displays the name of the package and a Git endpoint. You will need either the name or Git endpoint to install a package.

### Installing packages

To add a new Bower package to your project you use the install command. This should be passed the name of the package you wish to install.
```
$ bower install [package]
```
As well as using the package name, you can also install a package by specifying one of the following:

* A Git endpoint such as git://github.com/components/jquery.git
* A path to a local Git repository.
* A shorthand endpoint like components/jquery. Bower will assume that GitHub is being used, in which case, the endpoint is the part after github.com in the repository URL.
* A URL to an archive (a `zip` or `tar` file). The files in the archive will be extracted automatically.

You can install a specific version of the package by adding a pound-sign (`#`) after the package name, followed by the version number.

```
$ bower install [package]#[version]
```
Installed packages will be placed in a bower_components directory. This is created in the folder which the bower program was executed. You can change this destination using the configuration options in the `.bowerrc` file.

Once installed, you can use a package by simply adding a `<script>` or `<link>` tag to your HTML markup. Although Bower packages most commonly contain JavaScript files, they can also contain CSS or even images.

```
<script src="path/to/bower_components/jquery/jquery.min.js"></script>
```
### Installing packages using a `bower.json` file

If you are using multiple packages within your project, it's often a good idea to list these packages in a `bower.json` file. This will allow you to install and update multiple packages with a single command.

*bower.json*
```
{
  "name": "app-name",
  "version": "0.0.1",
  "dependencies": {
    "sass-bootstrap": "~3.0.0",
    "modernizr": "~2.6.2",
    "jquery": "~1.10.2"
  },
  "private": true
}
```
The  example above shows a `bower.json` file which defines some information about the projects as well as a list of dependencies. The `bower.json` file is actually used to define a Bower package, so in effect you're creating your own package that contains all of the dependencies for your application.

The properties used in this example are explained below.

* **name** – The name of your application/package.
* **version** – A version number for your application/package.
* **dependencies** – The packages that are required by your application.
* **private** – Setting this property to true means that you want the package to remain private and do not wish to add it to the registry in the future.
* 
Once you've got your `bower.json` file set up you can simply run the `bower install` command to install all of the packages you have specified.

Bower includes a handy utility that will help you to create a `bower.json` file for your project. Running the `bower init` command from the root folder of your project will launch an interactive program that will create the file for you. However, you may still need to add some packages to the file yourself.


### Listing Installed Packages

You can easily find out which packages are installed using the `list` command.
```
$ bower list
```
The snippet below shows the output for a simple project that uses jQuery, Modernizr and Sass. Notice that Bower also does a check to see if a newer version of each of the packages is available.
```
bower check-new     Checking for new versions of the project dependencies...
HelloIonic /Users/thomas/CraftAcademy/solutions/cooper/cooper_client
├── Chart.js#1.1.1 (latest is 2.1.5)
├─┬ angular-animate#1.4.3 extraneous (latest is 1.5.7-build.4875+sha.9686f3a)
│ └── angular#1.5.6 incompatible with 1.4.3 (1.4.3 available, latest is 1.5.7-build.4875+sha.9686f3a)
├─┬ angular-chart.js#0.9.0 (latest is 1.0.0-alpha6)
│ ├── Chart.js#1.1.1 incompatible with ~1.0.1 (1.0.2 available, latest is 2.1.5)
│ └── angular#1.5.6 incompatible with 1.4.x (1.4.11 available, latest is 1.5.7-build.4875+sha.9686f3a)
├── angular-fusioncharts#0.0.2
├─┬ angular-resource#1.5.6 (1.5.7-build.4875+sha.9686f3a available)
│ └── angular#1.5.6 (latest is 1.5.7-build.4875+sha.9686f3a)
├─┬ angular-sanitize#1.4.3 extraneous (latest is 1.5.7-build.4875+sha.9686f3a)
│ └── angular#1.5.6
├─┬ angular-ui-router#0.2.13 extraneous (latest is 1.0.0-alpha.5)
│ └── angular#1.5.6 (1.5.7-build.4875+sha.9686f3a available)
├── ionic not installed
└─┬ ng-token-auth#0.0.28 (latest is 0.0.29)
  ├── angular#1.5.6 incompatible with ~1.4.4 (1.4.11 available, latest is 1.5.7-build.4875+sha.9686f3a)
  └─┬ angular-cookie#4.0.10 (latest is 4.1.0)
    └── angular#1.5.6 (1.5.7-build.4875+sha.9686f3a available)
```
### Updating Packages

Updating a package is pretty simple. If you've used a `bower.json` file you can run a simple update command to update all of the packages at once. However, the update tool will abide by the version restrictions you've specified in the `bower.json` file.
```
$ bower update
```
To update an individual package you again use the update command, this time specifying the name of the package you wish to update.

```
$ bower update [package]
```
### Uninstalling Packages

To remove a package you can use the `uninstall` command followed by the name of the package you wish to remove.

```
$ bower uninstall [package]
```
It's possible to remove multiple packages at once by listing the package names.
```
bower uninstall Chart.js ng-token-auth angular-resource
```

### Bower and Rails

After reading terms like CSS, Javascript and HTML, you are already having nightmares thinking at the idea of bringing a 3rd party tool into the dreaded asset pipeline but fear not as Bower and the asset pipeline actually play very well together. Without further ado, here's how to set the whole thing up.

#### Install Bower

Make sure you have bower installed (see above) 

#### Configuring Bower

By default Bower install the components (what it calls JS/CSS libraries) in `bower_components` folder, which doesn't really play with the standard Rails folder hierarchy. To change that, simply create a .bowerrc (bower's config file) file at the root of your Rails app and add this:

{
  "directory": "vendor/assets/bower_components"
}
This will tell Bower to save all of the component files in that directory which follow Rails' convention on storing assets.

### `package.json`

From the root of your Rails app, run `bower init` to create your `bower.json` file. 


Depending on your answers, your `bower.json` should look something like this:
```json
{
  name: 'MyApp',
  version: '0.0.1',
  authors: [ ],
  description: 'Your description',
  license: 'MIT',
  homepage: 'http://whatever.com',
  ignore: [
    '**/.*',
    'node_modules',
    'bower_components',
    'test',
    'tests'
  ],
  "dependencies": {
    "angular": "~1.2.16"
  }
}
```
Now if I run `bower install`in your terminal, it will pull Angular 1.2.16 from the Github repo into `vendor/assets/bower_components`.

#### Configuring Rails

Now that Bower is installed and working, you need to make sure that Rails play nice with it. 

To do so, head to your `config/application.rb` file to let the asset pipeline about it. Simple add the following line to your file and save it.
```ruby
config.assets.paths << Rails.root.join('vendor', 'assets', 'components')
```

#### Including assets

To require the components downloaded using Bower, open up `app/assets/javascripts/application.js` and require the necessary JS file like you would usually do. In the example with Angular, you do so by adding this one line to it.
```
//= require angular/angular
```
For CSS frameworks and libraries like Bootstrap or Foundation, you can pop in the following line to `app/assets/css/application.css`.
```
*= require bootstrap/dist/css/bootstrap
```
That's it! You can now use Bower to manage all of the 3rd party CSS & Javascript libraries you want to use in your project and Rails' asset pipeline will take care of the rest for you.





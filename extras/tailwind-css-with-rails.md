How to Setup Tailwind css with rails

### Installation

First we need to install webpacker, we begin with adding webpacker to our Gemfile

```
# Gemfile

gem 'webpacker', '~> 3.4'
```

next we need to run bundle and install webpacker

```
$ bundle install

$ bundle exec rails webpacker:install
```

Now we can start on setting up tailwind for our project by running in our terminal

```
$ yarn add tailwindcss
```

Next we need to create a folder to contain ?webpacker and tailwindcss files?

```
$ mkdir app/javascript/css
```

We need to create a configuration file for tailwind and we run from node\_modules tailwind init.

```
$ ./node_modules/.bin/tailwind init app/javascript/css/tailwind.js
```

This file \`tailwind.js\` is where default css configuration for tailwind resides and you have free reign to go in and modify it. Some of the things you can modify are

```
colors

fonts

textsizes

screensizes
```

Now we will create our custom css configuration file.

```
 touch app/javascript/css/application.css
```

And we paste into that file the example stylesheet from tailwindcss.

    /**
    * This injects Tailwind's base styles, which is a combination of
    * Normalize.css and some additional base styles.
    *
    * You can see the styles here:
    * 
    https://github.com/tailwindcss/tailwindcss/blob/master/css/preflight.css

    *
    * If using `postcss-import`, use this import instead:
    *
    * @import "tailwindcss/preflight";
    */
    @tailwind preflight;
    /**
    * This injects any component classes registered by plugins.
    *
    * If using `postcss-import`, use this import instead:
    *
    * @import "tailwindcss/components";
    */
    @tailwind components;
    /**
    * Here you would add any of your custom component classes; stuff that you'd
    * want loaded *before* the utilities so that the utilities could still
    * override them.
    *
    * Example:
    *
    * .btn { ... }
    * .form-input { ... }
    *
    * Or if using a preprocessor or `postcss-import`:
    *
    * @import "components/buttons";
    * @import "components/forms";
    */
    /**
    * This injects all of Tailwind's utility classes, generated based on your
    * config file.
    *
    * If using `postcss-import`, use this import instead:
    *
    * @import "tailwindcss/utilities";
    */
    @tailwind utilities;
    /**
    * Here you would add any custom utilities you need that don't come out of the
    * box with Tailwind.
    *
    * Example :
    *
    * .bg-pattern-graph-paper { ... }
    * .skew-45 { ... }
    *
    * Or if using a preprocessor or `postcss-import`:
    *
    * @import "utilities/background-patterns";
    * @import "utilities/skew-transforms";
    */

Now we need to import our custom css file into webpacks application.js file

```
# app/javascript/packs/application.js
import "application.css";
```

Now we need to add javascipt pack tag and stylesheet pack tag into our main layout file.

```
# app/views/layouts/application.html.haml

= javascript_pack_tag 'application'

= stylesheet_pack_tag 'application'
```

Next we need to modify a webpacker configuration file .postcssrc.yml which resides in our root project folder. This file is where we define tailwind css and it will allow us to use the @tailwind directive to inject Tailwind's preflight and utilities styles into your CSS. We need to point it to the tailwind.js file that we created earlier and it should look like this.

```
# .postcssrc.yml

plugins:

  postcss-import: {}

  postcss-cssnext: {}

  tailwindcss: './app/javascript/css/tailwind.js'
```

### 

### useful links

Tailwind default documentation - https://tailwindcss.com/docs/what-is-tailwind/

Github repo that contains a lot of useful guides and demos for tailwind - https://github.com/merchedhq/awesome-tailwindcss


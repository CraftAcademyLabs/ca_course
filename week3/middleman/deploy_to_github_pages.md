---
title: "Deploy to Github pages"
subtitle: "Middleman"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Middleman, Github"
keywords: [Middleman, Github]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Deploy to Github pages

Now that you are done building your portfolio, it's time to publish it and brag about it to your friends.

First thing you need to do if you haven't already is create a repository for your project on Github. For the purpose of this walkthrough, we'll assume our repository on Github is `my_portfolio`.

There are numerous ways of setting up deployment of middleman sites. We'll make use of the gem [middleman-deploy](https://github.com/middleman-contrib/middleman-deploy) to get this done. A quick search on the internet will show you other ways of deploying your application if your interested.

### Setup the project

Head over to your Gemfile, and add the following line to it then run `bundle install`

```ruby
# Gemfile

gem 'middleman-deploy', '= 2.0.0.pre.alpha'
```

Next, in your `config.rb` file, add the following code bloc.

```ruby
# config.rb

activate :deploy do |deploy|
  deploy.build_before = true
  deploy.deploy_method = :git
end
```

There have been some reports of troubles while publishing the site to Github using the gem. Instead of deploying the content of the `build` directory to the `gh-pages`, where the site is served, the entire source code of the site is pushed. A fix to this issue involves removing the `build` directory before each deploy. We'll create an extension to take care of that for us as suggested [here](https://github.com/middleman-contrib/middleman-deploy/issues/114).


Create a new file `extensions/build_cleaner.rb` and add the following content to it:

```ruby
# extensions/build_cleaner.rb

class BuildCleaner < Middleman::Extension
  def initialize(app, options_hash = {}, &block)
    super
    FileUtils.rm_rf app.config[:build_dir]
  end
end

::Middleman::Extensions.register(:build_cleaner, BuildCleaner)
```

Now let's activate the extension in the `config.rb` file. Add the following code within the `:build` block


```ruby
# config.rb

require 'extensions/build_cleaner'

configure :build do
  activate :relative_assets
  activate :build_cleaner
end

```

Great now we're all set to deploy the application.

### Deploying the site

With everything setup, all that's left to do is to run the following command to publish the site.

```shell
middleman deploy
```

And voila! Your site should now be accessible from the link:

 `https://your-github-username.github.io/my_portfolio`

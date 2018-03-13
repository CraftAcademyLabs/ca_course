In this guide we'll take a closer look at how to add support forInternationalization and localization in a Rails application, where to store translations, what localized views are, how to format dates and times, how to introduce pluralization rules and more.

### Introduction

Internationalization \(i18n\) and Localization \(i10n\) are two sides of the same coin, and refer to means of adapting computer software to different languages, regional differences and technical requirements of a target locale. **Internationalization** is the process of designing a software application so that it can be adapted to various languages and regions without engineering changes. **Localization** is the process of adapting internationalized software for a specific region or language by adding locale-specific components and translating text. Localization \(which is potentially performed multiple times, for different locales\) uses the infrastructure or flexibility provided by internationalization \(which is ideally performed only once, or as an integral part of ongoing development\).

### What's up with the abbreviations?

Well, what do those strange acronyms actually mean? In fact they are abbreviations composed of the first and last letter of a word, and in between there is the number of letters.

* **i18n** = Internationalization: i, then 18 letters, then n

* **l10n** = Localization: l, then 10 letters, then n

Moving on...

### I18n in RoR

In Rails-world, we most often use the term i18n for all activities related to translating an application. A bit sloppy perhaps.

Out of the box, if you scaffold a new Rails application it provides support for English, making it easy to customize and extend everything for other languages.

These Rails API features are covered by Rails 5:

* looking up translations
* interpolating data into translations
* pluralizing translations
* using safe HTML translations \(view helper method only\)
* localizing dates, numbers, currency, etc.

Generally it is very simple to localize your app by using the `t`\(`translate`\) helper and `.yml`** **files. The default `en.yml` locale in the `config/locales` directory contains a sample pair of translation strings. With the `l`\(`localize`\) helper you can localize date and time objects to local formats.

Here is an example of how to use `t()` and `l()`

---

### Untranslated view

```ruby
<h1>Hello World</h1>
<p><%= Time.now %></p>
```

### Localized view

```ruby
<h1><%= t 'hello_world' %></h1>
<p><%= l Time.now %></p>
```

### Adding locales to your application

Getting started with I18n in Rails is simple. One of the first things we want to do is to set up the available locales in our application class in `config/application.rb`.

!FILENAME config/application.rb

```ruby
module NewsRoom
  class Application < Rails::Application
    config.i18n.available_locales = [:se, :en]
    config.i18n.default_locale = :en
    # more code...
```

![](/assets/i18n_application_rb.png)

As the next step, create a YAML file with the correct locale as root key** **in the `config/locales` directory. You can name it after the language’s ISO code, e.g. `se.yml`.

```
sv:
  hello: "Hallå Världen"
```

Rails adds a `t (translate)` helper method to your views so that you do not need to spell out `I18n.t()` all the time. Also, this helper will catch missing translations and wrap the resulting error message into a `%span{class: 'translation_missing}` \(rendered into: `<span class="translation_missing">`\). Use the i18n keys in your views or controllers, like this:

View:

```ruby
%h1= t('hello')
```

Controller

```ruby
flash[:notice] = t('hello')
```

We're not quite where we want to be yet, though. If you would try to access the `sv` locale, you notice that the application renders the English translation. Enter the `rails-i18n` gem that provides locale data for Ruby and Rails. It helps us to stores basic translations like months’ and years’ names, validation messages, pluralization rules and many other ready to use stuff.

Add `gem 'rails-i18'` to your `Gemfile` and bundle up.

### Switching locales

In order to allow your users to switch locales, you need to modify your `ApplicationController` and add a `before_action` that will be rune before every action in your application \(or at least all actions in controllers that inherit the `ApplicationController`\)

!FILENAME config/application.rb

```ruby
before_action :set_locale

def set_locale
  I18n.locale = params[:locale] || I18n.default_locale
end
```

If you set the locale to Swedish using a query parameter  \(`http://localhost:3000?locale=sv`\), the response renders the translated strings. The same thing applies to the `en` locale. But what would happen if you try to pass in a locale that is not listed in your `I18n.available_locales`?. Let's try with passing in `es` for Spanish:

![](/assets/I18n_error_message_invalid_locale.png)

_Note that in this example, I'm using the _[_HighVoltage gem_](https://github.com/thoughtbot/high_voltage)_ that allows us to render static pages in a Rails application. OI will be covering usage of this gem in a separate guide. _

There is a fix we can apply to avoid this. We can modify our `set_locale` method in the `ApplicationController` to check if the passed in locale is listed in our configuration and apply a fallback.

```ruby
  def set_locale
    I18n.locale = I18n.available_locales.include?(params[:locale]&.to_sym) ? params[:locale] : I18n.default_locale
  end
```

We use a ternary operator to check if the locale passed in by params \(if there is such a param in the request\) is listed as a valid locale in our app configuration before we set the `I18n.locale`. If it's not, we fall back on the `default_locale` of our application. 

### RESTful routes

Passing in locale settings in query params is okay, but having your application respond to more RESTful routes is even better. Just ask any overpaid SEO expert on how the Google bots like to see the your application URL's. 

Let's see if we can move away from this \(in development. In production `localhost:3000` will, of course, be replaced by your host's domain\):

```
http://localhost:3000?locale=sv
http://localhost:3000?locale=en
```

To this:

```
http://localhost:3000
http://localhost:3000/sv
http://localhost:3000/en
```

In order to achieve this we need to make some modification to our route table and the `set_locale` method in our `ApplicationController`. by adding a `scope` to all routes. 

!FILENAME config/routes.rb

```ruby
Rails.application.routes.draw do
  scope '(:locale)', locale: /en|sv/ do
    #Point your 'root_path' to whatever controller and action you want
    root controller: :articles, action: :index 
  end
end
```

In the `set_locale` method, we'll make sure every request will be 

!FILENAME app/controllers/application\_controller.rb

```ruby
class ApplicationController < ActionController::Base
  before_action :set_locale
  private
  def set_locale
    I18n.locale = I18n.available_locales.include?(params[:locale]&.to_sym) ? params[:locale] : I18n.default_locale
    Rails.application.routes.default_url_options[:locale] = I18n.locale
  end
end
```

### More resources

https://github.com/enriclluelles/route\_translator




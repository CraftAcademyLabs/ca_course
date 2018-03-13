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

![](/assets/i18n_application_rb_2t.png)

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

Controller:flash

```ruby
flash[:notice] = t('hello')
```

### Switching locales

In order to allow your users to switch locales, you need to modify your `ApplicationController` and add a `before_action` that will be rune before every action in your application \(or at least all actions in controllers that inherit the `ApplicationController`\) 

```ruby
before_action :set_locale

def set_locale
  I18n.locale = params[:locale] || I18n.default_locale
end
```

If you set the locale to Swedish using a query parameter  \(`http://localhost:3000?locale=sv`\), the response renders the translated strings.

![](/assets/I18n_application_controller_2.png)




First you want to add the `haml` gem to the Gemfile

```ruby
gem 'haml'
```

After that you need to run bundle.

Now you need to convert the layout file that was scaffolded for you when you initialized this middleman project. The layout file should look and be named like this:

```haml
# source/layouts/layout.html.haml

!!!
%html
  %head
    %meta{:content => "text/html; charset=UTF-8", "http-equiv" => "Content-Type"}/
    %meta{:charset => "utf-8"}/
    %meta{:content => "ie=edge", "http-equiv" => "x-ua-compatible"}/
    %meta{:content => "width=device-width, initial-scale=1, shrink-to-fit=no", name: "viewport"}/
    / Use the title from a page's frontmatter if it has one
    %title= current_page.data.title || "Middleman"
    = stylesheet_link_tag "site"
    = javascript_include_tag "site"
  %body
    = yield

```

The index file that was scaffolded also needs to be updated. The file should look and be named like this:

```haml
# source/index.html.haml

!!!
%html
  %svg.middleman-logo{"aria-labelledby" => "middleman-logo__title", role: "img", viewbox: "0 0 400 340", xmlns: "http://www.w3.org/2000/svg"}
    %title#middleman-logo__title Middleman
    %path.middleman-logo__top-left-bar{d: "M0 40L200 0v30L0 60z", "fill-opacity" => ".45"}
    %path.middleman-logo__top-right-bar{d: "M200 0l200 40v20L200 30z", fill: "#fff"}
    %path.middleman-logo__left-m{:d => "M0 78v184l45 5V152l45 83 47-83v129l53 7V52l-57 8-43 83-43-70z", "fill-opacity" => ".45"}
    %path.middleman-logo__right-m{:d => "M400 78v184l-45 5V152l-45 83-47-83v129l-53 7V52l57 8 43 83 43-70z", fill: "#fff"}
    %path.middleman-logo__bottom-left-bar{d: "M0 300l200 40v-30L0 280z", "fill-opacity" => ".45"}
    %path.middleman-logo__bottom-right-bar{d: "M200 340l200-40v-20l-200 30z", fill: "#fff"}
  %h1
    Middleman is Running
  = link_to(                                                  |
      "Read Documentation",                                   |
      "https://middlemanapp.com/basics/templating_language/", |
      target: "_blank"                                        |
    )      
```

## Partials

Partials are a way of sharing content across pages and helps you to keep your code DRY. They can be used both in templates and in layouts.

Let's assume that we want to display a footer on our page.

Under the `= yield` command in your main layout file, place the call to render the footer partial. 

!FILENAME layouts/layout.html.haml
```haml
= partial 'partials/footer'
```



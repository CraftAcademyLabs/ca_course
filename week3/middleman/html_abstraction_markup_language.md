---
title: "HAML - HTML abstraction markup language"
subtitle: "Middleman"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Middleman, HAML"
keywords: [Middleman, HAML]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## HAML - HTML abstraction markup language

At its core, Haml is a lightweight markup language. Haml makes writing HTML much easier, and when we say **"much easier"** what we really mean is **"you won't believe how much time you can save by using Haml."** With Haml you will immediately see the intense time saving benefits that it brings to the table.

HTML:


```html
<p>This is an HTML paragraph</p>
```
Notice how even the simplest bit of HTML involves some repetition. The paragraph tag must be placed at the beginning of the statement, and then a corresponding closing tag must be placed at the end. What if we could skip all of this nonsense?

Here's the same statement in Haml:

```haml
%p This is also an HTML paragraph
```

In Haml, we prefix HTML tags with a "%" and it takes care of the rest. But wait you implore, how does one differentiate between two elements without the closing tag? The answer is of course that Haml is whitespace dependent, meaning it uses the visual structure of your code to determine the HTML output. Here's another example that uses multiple elements and even nests some elements inside of others (the list items are nested in the unordered list).

Haml:

```haml
%h2 This is a Headline
%a This is a link.
%ul
	%li List item one
	%li List item two
	%li List item three
%p Another paragraph can go here.
```
This markup will render:
```html
<h2>This is a Headline</h2>
<a>This is a link.</a>
<ul>
  <li>List item one</li>
  <li>List item two</li>
  <li>List item three</li>
</ul>
<p>Another paragraph can go here.</p>
```

### Classes and IDs

Without the traditional tag structure, you might be wondering how to declare extra HTML items like classes and IDs. Haml is way ahead of you and even uses a syntax that you'll find oddly familiar.

Haml:

```haml
%ul.someClass
	%li#someID
```

As you can see, the "." and "#" symbols are used to denote classes and IDs respectively. This is great because you're already familiar with this method in CSS. That familiarity means that once you really dive in, you'll be able to pick up Haml in no time. Here's how this code compiles.

HTML:

```html
<ul class='someClass'>
  <li id='someID'></li>
</ul>
```

### Divs Are Magic

Let's face it, even with all the fancy new HTML5 elements, we still all love our divs. Given that this particular element is so common, Haml has assigned it a particularly simple syntax. Consider how you think you might write the following HTML in Haml.

HTML:

```html
<div id="someDiv">
</div>
```
Given what we've learned so far, you'll no doubt reach the conclusion that some form of “%div” is necessary here. However, Haml allows you to skip the div syntax all together. To write the above HTML in Haml, this is all you need.

Haml:

```haml
#someDiv
```
That's it! This code compiles to a complete div with an ID of `someDiv`. Even the skeptics among you have to admit that this is pretty dang concise. Once again we see the clear benefits of being able to write our markup like we write CSS.

### More complex views
Just to make sure you've really gotten the gist of how this works, here's a much more extensive example. This time we'll use one main div and two inner divs along with plenty of other elements.

Haml:

```haml
#container
  .box
    %h2 Some Headline
    %p Lorem ipsum doller your mom...
    %ul.mainList
      %li One
      %li Two
      %li Three

  .box
    %h2 Some Headline
    %p Lorem ipsum doller your mom...
    %ul.mainList
      %li One
      %li Two
      %li Three​​​
```
HTML:

```html
<div id='container'>
  <div class='box'>
    <h2>Some Headline</h2>
    <p>Lorem ipsum doller your mom...</p>
    <ul class='mainList'>
      <li>One</li>
      <li>Two</li>
      <li>Three</li>
    </ul>
  </div>
  <div class='box'>
    <h2>Some Headline</h2>
    <p>Lorem ipsum doller your mom...</p>
    <ul class='mainList'>
      <li>One</li>
      <li>Two</li>
      <li>Three</li>
    </ul>
  </div>
</div>
```
This time around you can really start to feel the awesome power of Haml.

The charm here goes beyond the fact that we've saved tons of characters and even a few entire lines, notice how much easier it is to read the Haml at a glance. All of the nasty clutter that comes with HTML has bee stripped away and in its place is a clear hierarchy of markup that is instantly discernible.

### HTML5

Just in case you're wondering, Haml works just fine with all of the cool new tags inside of HTML5. In fact, it doesn't really care what type of tags you try to create. If you type “%somecrazytag” the output result will be ““ regardless of the fact that this isn't even valid HTML.

Here's a quick example to illustrate that uses the new header, `nav`, section and footer HTML5 tags.

Haml:

```haml
.wrapper
  %header
    %nav
      %ul
        %li one
        %li two
        %li three

  %section
    .info
    %p Lorem ipsum doller set

  %section
    .info
    %p Lorem ipsum doller set
  %section
    .info
    %p Lorem ipsum doller set

  %footer
    %p Lorem ipsum doller set​​
```
HTML:

```html
<div class='wrapper'>
  <header>
    <nav>
      <ul>
        <li>one</li>
        <li>two</li>
        <li>three</li>
      </ul>
    </nav>
  </header>
  <section>
    <div class='info'></div>
    <p>Lorem ipsum doller set</p>
  </section>
  <section>
    <div class='info'></div>
    <p>Lorem ipsum doller set</p>
  </section>
  <section>
    <div class='info'></div>
    <p>Lorem ipsum doller set</p>
  </section>
  <footer>
    <p>Lorem ipsum doller set</p>
  </footer>
</div>
```

## Outputting Ruby in HAML

So how do we embed Ruby in our HAML? In ERB we might have:

```html
<p class='flash'><%= flash[:notice] %></p>
```
Given what you've seen from the `H1`, you would imagine the `<p></p>` becomes `%p`. But what about the Ruby injection?

### Ruby Injections

HAML's approach is to reduce `<%= %>` to just `=`. The HAML engine assumes that if the content starts with an `=`, that the entire rest of the line is Ruby. For example, the flash paragraph above would be rewritten like this:

```haml
%p= flash[:notice]
```
Note that the `=` must be placed against the `%p` tag.

### Adding a CSS Class

But what about the class? There are two options. The verbose syntax uses a hash-like format:

```haml
%p{class: 'flash'}= flash[:notice]
```
But we can also use a CSS-like shorthand syntax:

```haml
%p.flash= flash[:notice]
```
The latter is easier and more commonly used.

### Mixing Plain Text and Ruby

Consider a chunk of content that has both plain text and Ruby like this:

```erb
<div id="sidebar">
Filter by Tag: <%= tag_links(Tag.all) %>
</div>
```
Given what we've seen so far, you might imagine it goes like this:

```haml
%div#sidebar Filter by Tag: = tag_links(Tag.all)
```
But HAML won't recognize the Ruby code there. Since the element's content starts with plain text, it will assume the whole line is plain text.

#### Breaking Ruby and Text into Separate Lines

One solution in HAML is to put the plain text and the Ruby on their own lines, each indented under the DIV:

```haml
%div#sidebar
  Filter by Tag:
  = tag_links(Tag.all)
```
Since version 3, HAML supports an interpolation syntax for mixing plain text and Ruby:

```haml
%div#sidebar
  Filter by Tag: #{tag_links(Tag.all)}
```
And it can be pushed back up to one line:

```haml
%div#sidebar Filter by Tag: #{tag_links(Tag.all)}
```

### Ruby commands

We've seen plain text, HTML elements, and printing Ruby. Now let's focus on non-printing Ruby.

One of the most common uses of non-printing Ruby in a view template is iterating through a collection. In ERB we might have:

```erb
<ul id='articles'>
  <% @articles.each do |article| %>
    <li><%= article.title %></li>
  <% end %>
</ul>
```
The second and fourth lines are non-printing because they omit the equals sign. HAML's done away with the <%. So you might be tempted to write this:

```haml
%ul#articles
  @articles.each do |article|
    %li= article.title
```
Content with no marker is interpreted as plain text, so the `@articles` line will be output as plain text and the third line would cause a parse error.

### Marking Non-Printing Lines

We need a new symbol to mark non-printing lines. In HAML these lines begin with a hyphen `(-)`:

```haml
%ul#articles
  - @articles.each do |article|
    %li= article.title
```
No `end`!

What about the `end` that we use in our ruby files and in erb? HAML uses that significant whitespace to reduce the syntax of HTML and Ruby. The end for the do is not only unnecessary, **it will raise an exception if you try to use it.**

## Conclusion
HAML templates are cleaner, easier to read, easier to maintain, and easier to develop on.

An anonymous developers opinion will close this section:


> HAML makes me faster. A lot faster. Not having to track down where a closing tag was omitted, never accidentally crossing tag scope, and being able to trivially ascertain the selector path of a given bit of markup makes templating fast. I do a lot of it "the old way" too, and...it's just not as fun. It's frustrating, and slow, and it's not because I'm bad at it - I did it for years before HAML - it's because if you take the time to learn it, HAML is genuinely better.

And another one:

> My love for haml was formed in about... 5 minutes. I took an existing template (had some free time) and ported it to haml. After eliminating about 30% of the typed characters/lines on that template, I realized how much less work it was to create a haml template vs html. Less maintenance, less syntactic bloat, the content just flowed. Mind you I have been writing HTML for 4 years now without any problems. I no longer had debates (do I add a tag here because I really don't f-ing feel like typing it) because it was trivial. It let me focus on content rather than the html. Also zero possibility of someone screwing up indentation thus causing a 100 line file to become completely unmaintainable. Which happens VERY often in html unless the writer is disciplined to all hell. Also: less characters + cleaner syntax = significantly easier to comprehend.

**Happy HAML coding!**

## HAML - HTML abstraction markup language

At its core, Haml is a lightweight markup language. Haml makes writing HTML much easier, and when we say **“much easier”** what we really mean is **“you won’t believe how much time you can save by using Haml.”** With Haml you will immediately see the intense timesaving benefits that it brings to the table.

HTML:


```html
<p>This is an HTML paragraph</p>
```
Notice how even the simplest bit of HTML involves some repetition. The paragraph tag must be placed at the beginning of the statement, and then a corresponding closing tag must be placed at the end. What if we could skip all of this nonsense? 

Here’s the same statement in Haml:

```haml
%p This is also an HTML paragraph
```

In Haml, we prefix HTML tags with a “%” and it takes care of the rest. But wait you implore, how does one differentiate between two elements without the closing tag? The answer is of course that Haml is whitespace dependent, meaning it uses the visual structure of your code to determine the HTML output. Here’s another example that uses multiple elements and even nests some elements inside of others (the list items are nested in the unordered list).

Haml:

```haml
%h2 This Is a Headline
%a This is a paragraph.
%ul
	%li List item one
	%li List item two
	%li List item three
%p Another paragraph can go here.
```
This markup will render:
```html
<a>This is a paragraph.</a>
<ul>
  <li>List item one</li>
  <li>List item two</li>
  <li>List item three</li>
</ul>
<p>Another paragraph can go here.</p>
```

###Classes and IDs

Without the traditional tag structure, you might be wondering how to declare extra HTML items like classes and IDs. Haml is way ahead of you and even uses a syntax that you’ll find oddly familiar.

Haml:

```haml
%ul.someClass
	%li#someID
```

As you can see, the “.” and “#” symbols are used to denote classes and IDs respectively. This is great because you’re already familiar with this method in CSS. That familiarity means that once you really dive in, you’ll be able to pick up Haml in no time. Here’s how this code compiles.

HTML:

```html
<ul class='someClass'>
  <li id='someID'></li>
</ul>
```

###Divs Are Magic

Let’s face it, even with all the fancy new HTML5 elements, we still all love our divs. Given that this particular element is so common, Haml has assigned it a particularly simple syntax. Consider how you think you might write the following HTML in Haml.

HTML:

```html
<div id="someDiv">
</div>
```
Given what we’ve learned so far, you’ll no doubt reach the conclusion that some form of “%div” is necessary here. However, Haml allows you to skip the div syntax all together. To write the above HTML in Haml, this is all you need.

Haml:

```haml
#someDiv
```
That’s it! This code compiles to a complete div with an ID of “someDiv”. Even the skeptics among you have to admit that this is pretty dang concise. Once again we see the clear benefits of being able to write our markup like we write CSS.

###More complex views
Just to make sure you’ve really gotten the gist of how this works, here’s a much more extensive example. This time we’ll use one main div and two inner divs along with plenty of other elements.

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
This time around you can really start to feel the awesomeone power of Haml. 

The charm here goes beyond the fact that we’ve saved tons of characters and even a few entire lines, notice how much easier it is to read the Haml at a glance. All of the nasty clutter that comes with HTML has bee stripped away and in its place is a clear hierarchy of markup that is instantly discernible.

###HTML5 

Just in case you’re wondering, Haml works just fine with all of the cool new tags inside of HTML5. In fact, it doesn’t really care what type of tags you try to create. If you type “%somecrazytag” the output result will be ““ regardless of the fact that this isn’t even valid HTML.

Here’s a quick example to illustrate that uses the new header, nav, section and footer HTML5 tags.

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









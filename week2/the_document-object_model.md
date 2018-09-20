---
title: "The Document-Object Model"
subtitle: "Week 2"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Javascript"
keywords: [Javascript]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
... 

# The Document-Object Model

The HTML DOM (Document Object Model)
_*"The W3C* Document Object Model (DOM) is a platform and language-neutral interface that allows programs and scripts to dynamically access and update the content, structure, and style of a document."*_

* World Wide Web Consortium

The HTML DOM is a standard object model and programming interface for HTML. It defines:

- The HTML elements as objects
- The properties of all HTML elements
- The methods to access all HTML elements
- The events for all HTML elements
When a web page is loaded, the browser creates a Document Object Model of the page.

The HTML DOM model is constructed as a tree of Objects:


![](https://github.com/CraftAcademy/ca_course/raw/master/Screen Shot 2016-11-16 at 1.11.09 PM.png){width=600px}




With the object model, JavaScript gets all the power it needs to create dynamic HTML:

JavaScript can:

- change all the HTML elements in the page
- change all the HTML attributes in the page
- change all the CSS styles in the page
- remove existing HTML elements and attributes
- add new HTML elements and attributes
- react to all existing HTML events in the page
- create new HTML events in the page

###Enabling JavaScript

Include javascript inside HTML:

```html
<script>
  x = 3;
</script>
```

Reference external file:
`<script src="http://example.com/script.js"></script>`

Not all browsers are JavaScript enabled. You might want to be prepared for that and redirect the user to a different page if JavaScript is disabled.

```html
<noscript>
    <meta http-equiv="refresh" content="0"; URL="http://example.com/noscript.html"/>
</noscript>
```

##Javascript Exercises #4
1. Open up a website (yes, any website). It might be easier to understand a very simple website - perhaps the one you created in the prep course. Select elements in the page by right-clicking on them and selecting "inspect".
2. Find a div. Give it a border. Change its background color. You can change any of its properties - font colors, sizes, and weights, colors, etc.
3. How would you hide an element? Show it again?
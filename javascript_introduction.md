# Javascript Introduction

Enabling JavaScript

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
    <meta http-equiv="refresh" content="0; URL=http://example.com/noscript.html"/>
</noscript>
```



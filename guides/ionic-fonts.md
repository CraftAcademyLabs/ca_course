---
title: "Guides"
subtitle: "Changing fonts and colors in Ionic with CSS"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Guides, Ionic"
keywords: [Guides, Ionic]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Changing fonts and colors in Ionic with CSS

If you dont want to use the standard font and colors in ionic, this is the guide for you. This guide assumes that you have an Ionic application scaffolded. 

## Changing the main font

First you need to create a fonts folder in assets. You find assets in the src folder.
In the terminal run:

 `$ mkdir src/assets/fonts`

Go to [Google Fonts](https://fonts.google.com/) and find a font that you want to use and download it. Extract the file from the zipped package you dowloaded. It will often contain several different versions of the font, but you only have to extract the one that says something with "`regular`" in the file name. In this gude we will use "[Montserrat](https://fonts.google.com/specimen/Montserrat)" as an example and it will be in the `woff2` format.

Add the font file to the `src/assets/fonts` folder.

Open upp the variables.scss and add this underneath the commented out code about `Shared Variables`.

```scss
@font-face {
    font-family: montserrat;
    src: url('../assets/fonts/montserrat.woff2') format('woff2');
}
```
Change the naming to match the font that you downloaded. 

If the font you dowloaded is an "ttf" file, then you need to create a folder within the fonts folder and put the file there. The format is also different, instead of `format('woff2')`, you need to have `format('truetype')`. 

e.g:
```scss
@font-face {
  font-family: montserrat;
  src: url('../assets/fonts/montserrat/montserrat.ttf') format('truetype');
}
```  
Now you want to add `$font-family-base: 'montserrat';` underneath the `@font-face`.

With all of this added, it should look like this:
```scss
// Shared Variables
// --------------------------------------------------
// To customize the look and feel of this app, you can override
// the Sass variables found in Ionic's source scss files.
// To view all the possible Ionic variables, see:
// http://ionicframework.com/docs/theming/overriding-ionic-variables/

@font-face {
  font-family: montserrat;
  src: url('../assets/fonts/montserrat.woff2') format('woff2');
}

$font-family-base: 'montserrat';
```

The `font-family-base` sets the font to be used as the base font for the entire application. This variable and many others can be found in the [link](http://ionicframework.com/docs/theming/overriding-ionic-variables/) in the commented out code about `Shared Variables`.

Now if you fire up the server, you should see the font being used instead of the basic ionic one.

## Okay, but what if you want to use more then one font for your ionic app?

Download the font you want to use and add it to the fonts folder, for this exapmle we will use [Oswald](https://fonts.google.com/specimen/Oswald).
In the `variables.scss`, add another `@font-face` to it with the new font. 

```scss
@font-face {
    font-family: oswald;
    src: url('../assets/fonts/oswald/Oswald-Regular.ttf') format('ttf');
}f
```
You can now call on that font in a class. Lets say we want a specific paragraph in our about page to use this other font. In the `about.sccs` we want to add a class for it. 

```scss
.oswald {
    font-family: oswald;
}
```

Then add that class to the paragraph in the `about.html` file and you should now see the other font being used when we run the server. 
## Changing the colors

If you open up the `variables.scss` in the `src/themes` folder and scroll down you will see code block about colors. In there you can change the the stadard ionic colors and you can add more color variables you can call on inside your elements.

Lets add pink to the colors:
```scss
$colors: (
  primary:    #488aff,
  secondary:  #32db64,
  danger:     #f53d3d,
  light:      #f4f4f4,
  dark:       #222,
  pink:       #f4f
);
```
Now you can call on it in elements like this:

`<button ion-button color="pink">Click Me</button>`

If you add this line of code somewhere and run the server, you should see a pink button. 

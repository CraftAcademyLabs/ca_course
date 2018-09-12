# Changing fonts and colors in Ionic with CSS

If you dont want to use the standard ionic font and colors, this is the guide for you. This guide assumes that you have an Ionic application scaffolded. 

## Fonts

First you need to create a fonts folder in assets. You find assets in the src folder.
In the terminal run:

 `mkdir src/assets/fonts`

Go to [Google Fonts](https://fonts.google.com/) and find a font that you want to use and download it. Extract the file from the zipped package you dowloaded. It will often contain several different versions of the font, but you only have to extract the one that says something with "`regular`". In this gude we will use "[Montserrat](https://fonts.google.com/specimen/Montserrat)" as an example and it will be in the `woff2` format. 

Open upp the app.scss, remove the commented out code and add this.
Change the name of the file to the one that you extracted from the dowloaded font package. 
```scss
@font-face {
    font-family: montserrat;
    src: url('../assets/fonts/montserrat.woff2') format('woff2');
}

* {
    font-family: montserrat;
}
```
The font you download may be in a other format called "`ttf`", the only thing you will have to do different if thats the case is to change the format when you set the url.

Add the font file to the `src/assets/fonts` folder.


### So what is happening here?

We first set the font at `@font-face` and set the src, where the application can find the font.

The second block of code is where we actually set the font to be used in the application. Everything in ionic inherits from the app component, so what we set in the `app.scss` will be used on every page in the application. The `*` means that all the elements in the `app.html` will have the attributes we set inside it.
 If we were to set that `*` inside another scss file like the  `home/home.scss`, that would mean that everything in `home.html` would have the attributes we give it.


If you fire up the server now with `ionic serve`, you will see that we have succeded in changing the font for our application.

### Okay, but what if you want to use more then one font for our ionic app?

Download the font you want to use and add it to the fonts folder, for this exapmle we will use [Oswald](https://fonts.google.com/specimen/Oswald).
In the `app.scss`, add another `@font-face` to it with the new font. 

```scss
@font-face {
    font-family: oswald;
    src: url('../assets/fonts/Oswald-Regular.ttf') format('ttf');

}
```

Dont remove any of the code we added before, just add this to it. Now we have to create a class were we use this font. Add this to the `app.scss`:

```scss
.oswald{
    font-family: oswald;
}
```

If you now try to call on this class on an element anywhere in the application, you will se that it does not work when you run the server. Thats because the "`*`" we set earlier overrides this class. So to make this class work you need to add `!important` to the it. The `.oswald` class should look like this in the `app.scss`:

```scss
.oswald{
    font-family: oswald !important;
}
```
Now you should be able to see the other font displayed in application. 

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








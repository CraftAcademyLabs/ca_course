## Styling and content

Let's move on with our Portfolio AND learn about React in the meantime. Don't forget the why we are doing this - having a portfolio site is fun, and that is why we chose building one for this exercise. But the real reason we are doing all of this is to learn to build engaging user interfaces with React. 

The endgame for this section of the course is this:

![](react_portfolio_2_tailwind_hello_world.png)

We want to add a header and a footer to our application, and we want to add a little bit of styling to it. With that foundation, we will be able to move on and add some content to our portfolio site.

## Create the page structure
If you look at the screenshot above, you can see that the application has changed since we left off in the previous section. We now have a header, a main content area (the "Hello World" part. Yes, we WILL remove it soon enough) and a footer section.

We will make the changes to our code by creating 2 new components (a `Header` and a `Footer` component), reference them from our `App` component and display them in our app. Finally, we will add a CSS framework ([TailwindCSS](https://tailwindcss.com/docs/what-is-tailwind/)) and style our application a bit. 

But first things first. Let's start with creating two nee files in our `src` folder. `Footer.jsx` and `Header.jsx`. In the first iteration, we can just add some basic text. We WILL style them a in a moment. 

This is the code for `Header.jsx`: 
```javascript
import React, { Component } from "react"

const Header = () => {
        return (
             <nav>
                <h1>My Portfolio</h1>
             </nav>
        )   
}

export default Header
```
And this is how we will define the component in `Footer.jsx`:
```javascript
import React from "react"

const Footer = () => {
        return (
            <footer>
                <h1>Made with React</h1>
            </footer>
        )
    
}

export default Footer
```

Consequently, we want to change the markup in our `App` component to include and reference our new components:

```javascript
import React from "react"
import ReactDOM from "react-dom"
import Hello from "./Hello"
import Header from "./Header"
import Footer from "./Footer"

const App = () => {
    return (
        <div>
            <Header />
            <Hello />
            <Footer />
        </div >
    )
};

ReactDOM.render(<App />, document.getElementById("app"))
```

Now, restart your development server to see the new components in action. They should be visible, but not impress you a lot. Right? It's time to add some styling to our application.

## Adding a CSS framework
We will make use of [TailwindCSS](https://tailwindcss.com/docs/what-is-tailwind/) to add a look and feel to our application. 

Let's start by adding Tailwind as an dependency first, then do a bit of configuration, and finally add some Tailwind classes to our components.

We use NPM to add Tailwind to our project. 

```bash 
$ npm i -D tailwindcss autoprefixer postcss-cli
```

The package comes with a script we can run to simplify the configuration process. 

```bash
$ npx tailwind init tailwind.config.js
```

_Note: `npx` is a tool intended to help round out the experience of using packages from the npm registry — the same way `npm` makes it super easy to install and manage dependencies hosted on the registry, `npx` makes it easy to use CLI tools and other executables hosted on the registry._

We also need to do some configuration for [PostCSS](https://postcss.org/) so we can use it to transform the Tailwind directives into pure CSS. Create a file called `postcss.config.js` in the root folder of your project, and add the following code (taken straight from the Tailwind docs):

```javascript
const tailwindcss = require('tailwindcss');
module.exports = {
    plugins: [
        tailwindcss('./tailwind.config.js'),
        require('autoprefixer'),
    ],
};
```

Now we need to set up our CSS entry point. We need to create a `src/css/tailwind.src.css` and use the `@tailwind` directive to insert Tailwind's preflight, utilities and screens styles into your CSS.

```css
@tailwind preflight;
@tailwind components;
@tailwind utilities;
@tailwind screens;

```

And finally, we need to modify the `scripts` section in our `package.json` to process our css and make sure to watch for changes so that we can continously see the output in our browser as we add Tailwind classes to our components. Take a close look at the code below BEFORE you make any changes/updates so that you understand what they do. Ww will add to new scripts (`build:css` and `watch:css`), but we also modify tou `start` and `build` scripts we added in previous section of this course. 

```json
"scripts": {
"build:css": "postcss src/css/tailwind.src.css -o src/css/tailwind.css",
"watch:css": "postcss src/css/tailwind.src.css -o src/css/tailwind.css -w",
"start": "npm run watch:css & webpack-dev-server --inline --mode development --open",
"build": "npm run build:css && webpack --mode production"
}
```

Before we commit our changes, there is one more detail to consider. Every time we run the `start` or `build` script, css will be compiled into a file named `tailwind.css` (note the differance in file names, we have `tailwind.src.css` where we can add our custom css classes, but the application will use `tailwind.css` at runtime). We want to exlude that file from version control. 

Modify your `.gitignore` and add the following line:
```
src/css/tailwind.css
```

## Styling

Tailwind comes with a LOT of predefined classes and can be a bit overwhelming. While writing this documentation, I did spend some time on [CodePen](https://codepen.io/) looking for some good UI examples to look at. I came up with the following styling that you are welcome to use (You can, of course, give your portfolio your own look and feel by compining the available classes on your own, or write some on your own). 

In `index.jsx` you can modify the `return` with the following markup:

```javascript
return (
        <div className="min-h-screen">
            <div className="flex flex-col w-full fixed pin-l pin-y">
                <Header />
                <div className="px-4 pt-4 flex-1 overflow-y-scrol">
                    <Hello />
                </div>
                <Footer />
            </div >
        </div >
)
```

We also need to import the css into that component.

```javascript
import './css/tailwind.css';
```

This is the code for the `return` in `Header.jsx`: 
```javascript
return (
        <nav className="flex items-center justify-between flex-wrap bg-blue-darkest p-6">
                <h1 className="text-white text-3xl">My Portfolio</h1>
        </nav>
)
    
```
And for `Footer.jsx`:
```javascript
return (
        <div class="border-t border-smoke px-8 py-4">
                <div class="flex justify-center text-grey">
                        Made with React
                </div>
        </div>
)
```

If you restart your development server you should be able to see the following:
![](react_portfolio_2_tailwind_hello_world.png)

## No quite there yet...
We are not quite done with our styling yet. Adding those Tailwind classes directly to our HTML tags is good while we develop our components, but becomes quite cumbersome to read and reuse. How about making life a bit simpler for ourselves? One thing we can do is to extract some of the classes into custom ones and make use of the `@apply` directive to bundle the Tailwind classes we used. 

Let's modify our `tailwind.src.css` with the following code:

```css
@tailwind preflight;
@tailwind components;
@tailwind utilities;

/** Page **/ 

.page-wrapper {
    @apply min-h-screen;
}
.page-content {
    @apply flex flex-col w-full fixed pin-l pin-y;
}

/** Header **/
.header {
    @apply flex items-center justify-between flex-wrap bg-blue-darkest p-6;
}

.header-title {
    @apply text-white text-3xl;
}

/** Footer **/

.footer-wrapper {
    @apply border-t px-8 py-4;
}

.footer-content {
    @apply flex justify-center text-grey;
}

/** Content section **/

.content-wrapper {
    @apply px-4 pt-4 flex-1 overflow-y-scroll;
}

.content-text {
    @apply text-grey-dark;
}

```

Finally we need to change the `return` in our components again. Go over the following code and make sure your look tha same (unless you have used any other classes in your implementation)

`index.jsx`

```javascript
return (
        <div className="page-wrapper">
            <div className="page-content">
                <Header />
                <div className="content-wrapper">
                    <Hello />
                </div>
                <Footer />
            </div >
        </div >
)
```

`Header.jsx`
```javascript
return (
        <nav className="header">
                <h1 className="header-title">My Portfolio</h1>
        </nav>
)
```

`Footer.jsx`
```javascript
 return (
        <div className="footer-wrapper">
                <div className="footer-content">
                        Made with React
                </div>
        </div>
)
```

And finally `Hello.jsx`
```javascript
return (
        <p className="content-text">Hello World</p>
)
```

This extraction will make things easier for us if we want to reuse some classes when we start to fill our application with more content. More about that in the next section.

## Wrap up
In this section, we started to set up the structure of our application by creating a few new components and displaying them on the page. We also added a CSS framework, gave our components a look and feel and made sure that we can reuse the classes. That's good progress - we are getting somewhere. 

In the next part, we will look into navigating within our portfolio (we will add an "About Me" and "My Projects" sections). 








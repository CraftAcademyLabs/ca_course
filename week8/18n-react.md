## Internationalization or I18N  in React

In this guide, we will show you how to add and use internationalization (I18N) in React.

### What is Internationalization?
Internationalization is a feature that makes our application ready to serve multiple languages for our users. We use the abbreviation I18n instead of writing out the long word inter..national...ization....... (where _18_ stands for the number of letters between the first _i_ and the last _n_ in the word _internationalization_.

### What is Localization?
Localization is the process of actually adding translations to the application. This is generally done by language experts rather than developers. Our job as developers is to make sure that the people doing the localization do not need to go into the source code to make their changes.  Just like with I18n we use the short L10n.

### Why do we use it?

There are several reasons for adding I18n to our applications. Here are some:
- We can serve customers in different parts of the world.
- Better user experience for people not speaking the domestic language. 
- The application is much more scalable and simpler to maintain in different languages.

### What can we not do with I18N?

We can not translate the actual content with I18n, only the layout of the application. This is often somewhat confusing for new developers. Basically what we do is to change the user interface of the application with I18n. For example, the menu, navbar, footer, button names, greeting messages and so on. Not the actual Articles, as they are saved in the database. Our changes are only made on the client-side of the application and only changes static content. 

To translate the actual content for example articles, we need to use machine learning and AI that understands what the text is actually about. 

### How to use the I18n in react
For us to use I18n we need to leverage on a package called I18n-react. This package will give us the ability to change the language in the application as well as autodetect and change the language automatically. We will go through both methods in this guide. Several other packages can help us with this. But we chose I18n for its ease of use.

### Setup

We are going to work with a very simple and basic application in this guide. This requires you to scaffold an application with at we will use two basic components. 

We want a component where we can change a greeting message depending on our language. Let's look at our components.

Our `App` component
```jsx
import React from "react";
import Greeting from "./components/Greeting";

const App = () => {
  return (
    <div>
      <Greeting />
    </div>
  );
};

export default App;
```
And our `Greeting` component
```jsx
import React from 'react'

const Greeting = () => {
  return (
    <div>
      <h1>Hello</h1>
      <h2>This is in English</h2>
    </div>
  )
}

export default Greeting
```
#### Adding packages
So let's add the necessary packages to our app, we use yarn for this project.

```bash
$ yarn add i18next-browser-languagedetector i18next i18next-xhr-backend
$ yarn add react-i18next 
```

Let's go over the packages we are adding
- `i18next` : internationalization-framework written in and for JavaScript
- `react-i18next` : internationalization-framework for React.js which is based on i18next
- `i18next-browser-languagedetector` : language detection plugin use to detect user language in the browser 
- `i18next-xhr-backend` : Plugin for detecting and use language files

#### Configuration file

Create the file `i18n.js` in the `src` folder.

```js
import i18n from 'i18next';
import Backend from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';

// the fallback language if the translations do not work.
const fallbackLng = ['en']; 

// Here we add the available languages we have Swedish and English 
const availableLanguages = ['en', 'sv']; 

 i18n
  .use(Backend) // load translation using xhr -> in the /public/locales.

   .use(LanguageDetector) // detect user language

   .use(initReactI18next) // pass the i18n instance to react-i18next.

   .init({
    fallbackLng, // if user computer language is not on the list of available languages, than we will be using the fallback language specified earlier
    debug: true,
    whitelist: availableLanguages,

     interpolation: {
      escapeValue: false
    },
  });
export default i18n;
```
In order to make the configuration file available to our app we need to import it to our `index.js` 

Add `import './i18n';` to the `index.js`


#### Language files
The next step we need to take is to create the language files. These will be in JSON format.
in your public folder create a subfolder called `locales` and inside that folder create two folders one for each language called `sv` and `en`.

Create your localization files  
```bash 
$ touch public/locales/sv/translation.json  
$ touch public/locales/en/translation.json
```
#### Suspense
It takes a few milliseconds for the language files to load. 
We will use a built-in React function called Suspense. This function will wait until the language files have loaded. Otherwise, it will display something that we want to display. We will add it to our `App.jsx` component.

```js
import React from "react";
import Greeting from "./components/Greeting";
import React, { Suspense } from "react";

const App = () => {
  return (
    <div>
      <Suspense fallback={<div>Loading</div>}>
        <Greeting />
      </Suspense>
    </div>
  );
};

export default App;
```

## Autodetect language
Add the translation functionality to the component where you want to change the content. In our case, it is the `Greeting` component. As we are using the `LanguageDetector` our app will automatically detect the right language that the user has set in their browser and serve the proper language.

```js
import React from 'react'
import { useTranslation } from "react-i18next";

const Greeting = () => {
// the t key is often standardized throughout the different frameworks. 
  const { t } = useTranslation();

  return (
    <div>
      <h1>{t('title')}</h1>
      <h2>{t('sub-title')}</h2>
    </div>
  );
}

export default Greeting
```

Lastly, we want to add the translations to our translation.json files

`public/locales/en/translation.json`
```json
{
  "title": "Hello",
  "sub-title": "This is in English"
}
```

And the Swedish translations
`public/locales/sv/translation.json`

```json
{
  "title": "HEJ",
  "sub-title": "Detta e svenska"
}
```
If you run `yarn start` in your terminal and visit `localhost:3000` you will see the application in Swedish (if you are based in Sweden and if you language settings are set to Swedish in the browser). But if you open an incognito window and visit `localhost:3000` you will see it in English.

Success!!

## Manually change the language

However, we also want to give our users the ability to manually change the language on our website. 

Let's add that final piece of functionality. 

```js
import Greeting from "./components/Greeting";
import React, { Suspense } from "react";
import i18n from "./i18n"; // Import the configurations

const App = () => {
  return (
    <div>
      <Suspense fallback={<div>Loading</div>}>
      
      // Add the button with the changeLanguage function and specify the language 
        <button onClick={() => {i18n.changeLanguage("en")}}>English</button>
        
      // Swedish as well 
        <button onClick={() => {i18n.changeLanguage("sv")}}>Swedish</button>
        <Greeting />
      </Suspense>
    </div>
  );
};

export default App;
```
The app can now serve multiple languages. 

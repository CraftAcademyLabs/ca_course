## Create a new component
`src/WeatherForecast.js`

```js
import React from 'react';

const WeatherForecast = () => {
  return (
    <>
      <h1>Its chilly</h1>
      <h1>Fantastic weather</h1>
    </>
  )
};

export default WeatherForecast
```

## Call it in the App component

```js
import React from 'react';
import WeaterForcast from './weatherForcast'

function App() {
  return (
    <>
      <WeaterForcast/>
    </>
  );
}

export default App;
```



## Add packages
`$ yarn add i18next-browser-languagedetector i18next i18next-xhr-backend`


## Add Config File

`scr/i18n.js`

```js
import i18n from 'i18next';
import Backend from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';

const fallbackLng = ['en'];
const availableLanguages = ['en', 'sv']; // Swedish and english or whatever you guys want

 i18n
  .use(Backend) // load translation using xhr -> see /public/locales.

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

Add `import './i18n';` to the `index.js`

## Add some suspense
Add suspense to the App Component
```js
import React, { Suspense } from 'react';

/// and

      <Suspense fallback={(<div>Loading</div>)}>
        <WeatherForecast/>
      </Suspense>
```

Create your loclization files in a folder locale
`public/locales/sv/translation.json` & `public/locales/en/translation.json`
```json
{
  "weather": {
    "title": "Vädret"
  },

   "news": {
    "title": "våra nyheter"
  }

 }
 ```

 ```json
{
  "weather": {
    "title": "Vädret"
  },

   "news": {
    "title": "våra nyheter"
  }
 }
 ```

```json
 {
    "news": {
      "title": "Its Chilly"
    },
    "weather": {
      "title": "Weather forecast Its Chilly"
    }
  }
```

### Add the translations
`import { useTranslation } from 'react-i18next';`

&&

```js
const WeatherForecast = () => {
  const { t } = useTranslation();
  return (
    <div>
      <h1>{t('weather.title')}</h1>
      <h1>{t('news.title')}</h1>
    </div>
  )
  ```

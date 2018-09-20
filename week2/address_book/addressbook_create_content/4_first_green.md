The first step in our scenario has to visit the homepage of our app. Let's update the step definition for this step as follow.

```javascript
Given('I visit the site', async function() {
  return await this.openHomePage()
})
```

`openHomePage()` is a helper method that should navigate to the home page of the app after opening a browser. This is where puppeteer comes into play. We have to use its API to create a browser page object and navigate to it.

All the helpers will be created in the `world.js` file inside the `features/support` directory.

```javascript
const { setWorldConstructor } = require('cucumber')
const { expect } = require('chai')
const puppeteer = require('puppeteer')

const HOME_PAGE = 'http://localhost:3000'

class AddressBookWorld {
  constructor() {}

  // Open the home page using puppeteer
  async openHomePage() {
    this.browser = await puppeteer.launch({headless: false, slowmo: 100})
    this.page = await this.browser.newPage()
    await this.page.goto(HOME_PAGE)
  }
}

setWorldConstructor(AddressBookWorld)
```

Now let's talk a bit about what's going on here.

The concept is world in cucumber is used to represent the application under test. In this case we create an `AddressBookWorld` and use the `setWorldConstructor` module from `cucumber` to initialize it.

The `HOME_PAGE` variable holds the url to the home page of our app.

In our `openHomePage()` helper method, we do the following:

1. Create a browser object using puppeteer

   `this.browser = await puppeteer.launch()`

2. Using the browser object, we create a new page

   `this.page = await this.browser.newPage()`

3. Once we have the page object we can now call the `goto` method on it, and pass the url we want to visit

> Note that we're using the keyword `await` before every call to puppeteer's objects. Most of the methods on provided as part of [puppeteer's API](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md) return Promise objects. `await` will wait for those promises to be resolved and will assign the response to each variable.
>
> Read more about `async` functions [at this link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).
>

Okay, now let's run cucumber again. The first step should now be green, but there's one small problem. The process stays idle after running all steps and doesn't exits as expected. The issue here is that we're opening a page but not closing it when we're done with it. When working with puppeteer's page object, you should always close the page when you finish working with it.

To fix this we'll create a new helper method in the `world.js` file to take care of that.

```javascript
class AddressBookWorld {
  ...

  async openHomePage() { ... }

  async closeHomePage() {
    await this.browser.close()
  }
}
```

Now we have to add a new step in our `basic-step.js` to close the page after every scenario.

```javascript
const { After, Given, Then, When } = require('cucumber')

After(async function() {
  return await this.closeHomePage()
})

...
```

Everything should now work as expected. Let's run cucumber one more time, the first step is green and the process exits as expected.

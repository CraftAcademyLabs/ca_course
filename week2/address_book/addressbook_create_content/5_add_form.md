### Check for content on the page

The next step we need to get to pass is `Then I should see "Contacts"`. This step expects the page to have a given content on it. After we visit the page, we should read it's text content and see if we can match the expected content. Puppeteer has a `content()` method on the page object that returns the HTML content of the page as a String. We can then use the Javascript [String.match()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match) function to match our content. 

Create the following helper in the `world.js` file

```javascript
 class AddressBookWorld {
  ...
  async openHomePage() { ... }
  async closeHomePage() { ... }

  async pageHasTextContent(expectedContent) {
    const pageContent = await this.page.content()
    const actualContent = pageContent.match(expectedContent)[0]

    expect(actualContent).to.be.eq(expectedContent)
  }
}
```

Now let's use the helper in the step definition

```javascript
Then('I should see {string}', async function(content) {
  return await this.pageHasTextContent(content)
})
```

Finally, let's add some content to our page.

```html
<body>
  <h1>Contacts</h1>
  <!-- ... -->
</body>
```

Now run cucumber, and the test should go green.

To fix the next step, all we have to do at this stage is also make sure we have the actual content on the page. This step also uses the same step definition we just created and just has a different content to search for on the page. 

```html
<body>
  <h1>Contacts</h1>
  <!-- add the following markup -->
  <div>
    <p>You have no contacts in your address book</p>
  </div>

  <!-- ... -->
</body>
```

Good progress so far, let's move to the next step.

### Click on buttons

The next step we have to fix is `When I click "Add contact"`.

Puppeteer allows you to select elements on a page using [CSS selectors](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Selectors). Keeping that in mind we're going to assume our button is going to have a CSS class `.add-contact`, we'll create a new helper to find and click on that button.

```javascript
class AddressBookWorld {
  ...
  async openHomePage() { ... }
  async closeHomePage() { ... }
  async pageHasTextContent(expectedContent) { ... }

  async clickOnAddContactBtn() {
    const btnSelector = '.add-contact'
    await this.page.waitForSelector(btnSelector)
    await this.page.click(btnSelector)
  }
}
```

`waitForSelector()` receives a selector as an argument and waits for it to appear on the page. When it does appear, we can use the `click()` method to click on the button.

We can now call this method in our step definition.

```javascript
When('I click {string}', async function(string) {
  return await this.clickOnAddContactBtn()
})
```

Let's run cucumber again. 

As you can see, we're not quite there yet. We get the following error on this step when running our tests.

```bash
✖ When I click "Add contact" # features/step_definitions/basic_steps.js:15
    Error: function timed out, ensure the promise resolves within 5000 milliseconds
```

One thing to note about cucumber-js is that the error messages are not as descriptive as we'd like them to be, be let's try to figure out what is wrong here.

Every promise that is returned from puppeteer will timeout after 5000 milliseconds by default if they can't be resolved. In this case, the request that's timing out is `waitForSelector()`. This function resolves when the specified element is added to the DOM. When we look at the `index.html` we can see that indeed we don't have a button for adding a contact on that page. Well, let's create one

```html
<body>
  <h1>Contacts</h1>
  <!-- add the button's markup -->
  <button class="add-contact">Add Contact</button>

  <div>
    <p>You have no contacts in your address book</p>
  </div>

  <!-- ... -->
</body>
```

Next up let's deal with the steps where we fill form fields with some content.

### Filling forms input

You may have picked up the flow by now on how we get every step to pass. First, let's create a helper method to find and fill forms input on our page.

```javascript
class AddressBookWorld {
  ...
  async openHomePage() { ... }
  async closeHomePage() { ... }
  async pageHasTextContent(expectedContent) { ... }
  async clickOnAddContactBtn() { ... }

  async fillFormField(field, content) {
    const inputSelector = `#contact-${field}`
    await this.page.waitForSelector(inputSelector)
    this.inputElement = await this.page.$(inputSelector)
    await this.inputElement.type(content)
  }
}
```

The selector we're looking for here is an ID selector in the form of `#contact-<field-name>`. So, for the `Name` field, we're expecting to have `#contact-name`.  The method is doing the following actions:

1. Create the `inputSelector` that holds the selector for our input field
2. Wait for the selector to be appear on the DOM
3. Use [page.$\(selector\)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pageselector) method to get the input element
4. Lastly, we call the `type()` method on the input element with the content we want to type in the field.

Next, we call the helper method in our step definition.

```javascript
Then('I fill in {string} with {string}', async function(field, content) {
  return await this.fillFormField(field.toLowerCase(), content)
})
```

If we run cucumber at this stage, we get another timeout error, suggesting we don't have the field we're looking for on our page. To fix that, we need to create a form with all the fields we need to create a contact.

```html
<body>
  <!-- ... -->

  <form action="#">
    <label for="name">Name</label>
    <input id="contact-name" name="name" type="text">
    <label for="email">Email</label>
    <input id="contact-email" name="email" type="email">
    <label for="phone">Phone</label>
    <input id="contact-phone" name="phone" type="text">
    <label for="company">Company</label>
    <input id="contact-company" name="company" type="text">
    <label for="notes">Notes</label>
    <textarea id="contact-notes" name="notes" type="text"></textarea>
    <label for="twitter">Twitter</label>
    <input id="contact-twitter" name="twitter" type="text">
  </form>
  <script src="./dist/bundle.js"></script>
</body>
```

Now run cucumber, and voila! all steps filling form fields are all green.

We've made good progress so far, but we need to fix a small issue before we move on.

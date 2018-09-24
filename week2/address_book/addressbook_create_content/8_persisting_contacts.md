We've come a long way with our application, what is left to do now is to actually store the contact and display it on the page. For storage, we've decided to keep things simple and make use of `localStorage`.

[LocalStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) is a [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API) property which will allow us to store key/value pairs that are saved across browser sessions. Remember one of the requirements for the application was to have the ability to view previously saved contacts. There are other alternatives that can be used to achieve this, but just to keep things simple, we will go with localStorage for this iteration of the application.

In order to save the contact we need to add the following snippet of code in the `submit` event listener we setup in the previous step. Add the code to `app.js`

```javascript
  addContactForm.addEventListener('submit', event => {
    event.preventDefault()
    const storage = window.localStorage
    ...
    console.log(`Saving the following contact: ${JSON.stringify(contact)}`)
    storage.setItem('contacts', JSON.stringify([contact]))
  })
```
Now, if you build this code, open the page and create a new contact, it should store that in localStorage in your browser. To verify this we're going to open the browser's developers tool and head to the Application tab. Look at the storage section, you'll see a Local Storage menu. Open that and select the url to your website \(which will likely be http://localhost:3000 since we're on our local machine\). You should then see the contact that you just stored as in the image below.
![Viewing the contact in DevTools](https://github.com/CraftAcademy/ca-course-week-2/blob/master/.gitbook/assets/screenshot-2018-06-14-17.21.52.png?raw=true)


Running our tests now, we still have the step verifying that we actually stored the contact pending. We need to implement the step definition for it and get it to pass. Head over to the `basic_steps.js` file and modify the following step with the code below:

```javascript
...
Then('I should have {int} contact in my address book', async function (contactCount) {
  return await this.checkContactStorageCount(contactCount)
})
```

Then we need to implement the `checkContactStorageCount` helper method in `world.js`


```javascript
async checkContactStorageCount(expectedCount) {
  const actualCount = await this.page.evaluate(
    () => JSON.parse(window.localStorage.getItem('contacts')).length
  )
  expect(actualCount).to.be.eq(expectedCount)
}
```

Let's go over what we're doing here. If you look back to when we stored the contact to our storage, we made use of the `setItem()` method. There's a `getItem()` method which is used to read items from `localStorage`. So what we do here, is call on that method to read an item named `contacts`. Given that the method returns a `string`, we have to parse that into a JSON object. Once we have the object we can get its `length` and store it in the variable `actualCount`.

The last step is to add an assertion that the `actualCount` is equal to the `expectedCount`. Remember our step here tests that we actually have 1 contact after submitting the form.

If we run our test now, that step should now go green. The last steps in our scenario are used to ensure that the contact we just created is displayed on the page. We will cover that in the next section.


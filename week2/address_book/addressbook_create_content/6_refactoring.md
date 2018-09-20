If you look at the output from running cucumber at the previous step, you'll note that the step `And I click "Save contact"` is also passing, even though we don't have a `Save contact` button on our page.

The reason for that is because it's re-using the step we created earlier on to click on the `Add contact` button. That step calls on the `clickOnAddContactBtn()` which was made to deal with the add contact button only. Additionally, we're not making use of the button name that is captured from the step written in our feature step.

Let's create a generic helper method that will be able to handle any buttons on the page. Replace the `clickOnAddContactBtn()` with the following method:

```javascript
class AddressBookWorld {
  ...
  async openHomePage() { ... }
  async closeHomePage() { ... }
  async pageHasTextContent(expectedContent) { ... }

  async clickOnButton(btnName) {
    const btnSelector = this.btnSelectorFromName(btnName.toLowerCase())
    await this.page.waitForSelector(btnSelector)
    await this.page.click(btnSelector)
  }

  async fillFormField(field, content) { ... }

  btnSelectorFromName(btnName) {
    switch (btnName) {
      case 'add contact':
        return '.add-contact'
        break
      case 'save contact':
        return '.save-contact'
        break
      default:
        throw `${btnName} button is not defined`
        break
    }
  }
}
```

Note that we've also added another helper method, `btnSelectorFromName()` that returns the proper selector for a button based on the name we provide.

If we run cucumber now, we'll note that the step where we click on the `Save contact` button is now failing, as it should have before with a timeout error. This is because we don't have the button on the page. Let's fix that right away by adding the button to the form.

```html
<form action="#">
  <!-- ... -->

  <input class="save-contact" type="submit" value="Save contact">
</form>
```

All green! Well... not quite. We have few steps left to deal with at this stage.

We've managed to set the stage for creating a contact with our test, but now we need to make sure that we're actually creating and persisting contact, and that, we can see the newly created contact on the page. We will deal with that in the next section.

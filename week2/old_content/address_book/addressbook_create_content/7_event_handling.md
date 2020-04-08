# Event handling

Right now, when we click on the save button, nothing happens. The reason for this is because we don't have any action registered to execute when the button is clicked, let's change that.

Add the following code in the `src/app.js` file. You can remove any code you have there at the moment.


```javascript
document.addEventListener('DOMContentLoaded', () => {
  // Select form object from the DOM
  const addContactForm = document.querySelector('.new-contact-form')

  // Register an event to listen for form submission
  addContactForm.addEventListener('submit', event => {
    // Disable default behavior when submitting form
    event.preventDefault()

    // Get all inputs elements from the form
    const {
      name,
      email,
      phone,
      company,
      notes,
      twitter,
    } = addContactForm.elements

    // Create contact object
    const contact = {
      id: Date.now(),
      name: name.value,
      email: email.value,
      phone: phone.value,
      company: company.value,
      notes: notes.value,
      twitter: twitter.value,
    }

    console.log(`Saving the following contact: ${JSON.stringify(contact)}`)
  })
})
```

Let's go over what we're doing here.

Before we can interact with anything on our page \(i.e clicking on buttons, filling forms, etc...\) we need to make sure that they're present on the DOM. This is what we use the `DOMContentLoaded` event for.

We add an event listener to the document to watch for `DOMContentLoaded` event. When the page loads and every element is drawn on the page, it will fire up and execute the callback function we've defined.

The first thing we do in the callback, is to select the form element and store it in a local variable. We use the [`document.querySelector()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) function here, which takes as argument a CSS Selector. In this case we pass in the class that's associated to the form. 

**Remember** to add the class `new-contact-form` to the  to the form element in your HTML file


**Also**, you have to run **`yarn build`** every time you make changes to your `app.js` file to generate a new `bundle.js` 

Or you can run **`yarn watch`** to watch for changes while you're building the functionalities of your application

Once we get the form element, we register an event listener for form submission. In the callback for this event, we proceed with reading values of all the form input fields and create a `contact` object. Now that we have the object, the last thing we're left to do is persist it. We will cover that in the next section.

#### Additional resources

* List of DOM events [https://developer.mozilla.org/en-US/docs/Web/Events](https://developer.mozilla.org/en-US/docs/Web/Events)
* List of event targets [https://developer.mozilla.org/en-US/docs/Web/API/EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)

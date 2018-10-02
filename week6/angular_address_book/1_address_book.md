## Address Book

We are going to be creating a Angular version of the address book we did in week 2

![address book screenshot](/images/address_book.png)


### Setup the application

We create the application by running the command `ng new` in the terminal.

```sh
$ ng new address-book
```

We will use Tailwind cdn for styling and we need to add the cdn link to the `index.html` file.

```html
# src/index.html

...
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss/dist/tailwind.min.css" rel="stylesheet">
</head>
```

Now we have the application setup and we can start on writing our tests. 

### E2E tests

We start by creating our e2e tests. And we have the following user story which we can use the foundation of our tests.

```
As a user
In order to stay in touch with my friends
I would like to be able to create a contact for them in my address book
```

Start by updating the existing beforeEach in `app.e2e-spec.ts` file and add ` page.navigateTo();` to it.

```js
# e2e/src/app.e2e-spec.ts

  beforeEach(() => {
    page = new AppPage();
    page.navigateTo();
  });
```

`page = new AppPage();` Here we are creating a new page-object called `page` and its that file we will need to write our element locators. A page object is an object-oriented class that serves as an interface to a page of your application. 

`page.navigateTo();` Here we are running the `navigateTo` function on the page object. And if you open up the `app.po.ts` file, located in the `e2e/src` folder, you will the function there.

```js
  navigateTo() {
    return browser.get('/');
  }
```

Now lets remove the it block that was generated for us and add these two tests instead.

```js

  it('should display create contact header', () => {
    expect(page.getParagraphText()).toEqual('Create contact');
  });

  it('should add a new contact', () => {
    let count = page.cardCount();
    expect(count).toBe(3);

    page.getField('name').sendKeys('John Doe');
    page.getField('email').sendKeys('john@craftacademy.se');
    page.getField('company').sendKeys('Craft Academy');
    page.getField('role').sendKeys('Tester');
    page.getField('twitter').sendKeys('@tester');
    page.getField('location').sendKeys('Gothenburg');
    page.getField('notes').sendKeys('Some tester guy');
    
    page.getSubmitButton().click();

    count = page.cardCount();
    expect(count).toBe(4);
  });
```

If you run the e2e tests now with `ng e2e` you will get this error message.

```sh
$ ng e2e
```


![Missing page functions](/images/missing_page_functions.png)

The reason for this error is that we have not created the functions we want to run.
So lets create those functions in `app.po.ts` file

```js
# e2e/src/app.po.ts

  getField(name) {
    return element(by.name(name));  // will locate a element by its name and return it
  }

  getSubmitButton() {
    return element(by.buttonText('Submit'));  // will return a button element that has text submit
  }

  cardCount() {
    return element.all(by.css('.card')).count();  // finds all elements with card class and returns how many they are
  }
```

Lets run our e2e tests now and get a different error.

```
$ ng e2e
```

And the error message should be

![Error message](/images/1st_run_e2e_tests.png)

Lets fix the first error message by adding the header Create contact, start by removing the content in `app.component.html` and add this instead.

```html

<h2>Create contact</h2>

```

Now run the e2e tests

```sh
$ ng e2e
```

And you will see this error message

![H1 not found](/images/h1_not_found.png)

I want you to fix this test before you move on. Once fixed running the e2e tests should give you only one error.


### Adding the contact form

Lets add this form to the `app.component.html` file, overwrite the header we already have.

```html
# src/app/app.component.html

<div class="container max-w-lg mx-6">
  <form class="bg-white shadow-md w-full max-w-md px-8 pt-6 pb-8 mb-4">
    <h2>Create contact</h2>
    <div class="flex flex-wrap -mx-3 mb-6">
      <div class="w-full px-3">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="name">
          Name
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4 mb-3"
          id="name" type="text" name="name" placeholder="John Doe">
      </div>
    </div>
    <div class="flex flex-wrap -mx-3 mb-6">
      <div class="w-full md:w-1/2 px-3 mb-6 md:mb-0">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-email">
          Email
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border rounded py-3 px-4 mb-3" id="grid-email"
          id="grid-email" type="email" name="email" placeholder="yo@company.com">
      </div>
      <div class="w-full md:w-1/2 px-3">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-email">
          Company
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4"
          id="grid-email" type="text" name="company" placeholder="Awesome company">
      </div>
    </div>
    <div class="flex flex-wrap -mx-3 mb-6">
      <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-role">
          Role
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4"
          id="grid-role" type="text" name="role" placeholder="Pure Genius">
      </div>
      <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-twitter">
          Twitter
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4"
          id="grid-twitter" type="text" name="twitter" placeholder="johndoe">
      </div>
      <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-location">
          Location
        </label>
        <input class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4"
          id="grid-location" type="text" name="location" placeholder="Pretoria">
      </div>
    </div>
    <div class="flex flex-wrap -mx-3 mb-2">
      <div class="w-full px-3">
        <label class="block uppercase tracking-wide text-grey-darker text-xs font-bold mb-2" for="grid-notes">
          Notes
        </label>
        <textarea class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4 mb-3"
          id="grid-notes" type="password" name="notes" placeholder="Things to know about this contact"></textarea>
      </div>
    </div>

    <div class="flex items-center justify-between">
      <button class="bg-orange hover:bg-orange-dark text-white py-2 px-4 rounded" type="button">
        Submit
      </button>
    </div>
  </form>
</div>
```

### Submit button

As it stands when we click on the Submit button nothing happens, lets change that by adding a click event to the button.

```html

  <button (click)="createNewContact()" class="bg-orange hover:bg-orange-dark text-white py-2 px-4 rounded" type="button">
    Submit
  </button>
```

Now we have added a click event that runs `createNewContact` functions when we press the submit button and we need to create that function in the component that this html file belongs to.

```js
# src/app/app.component.ts

...

export class AppComponent {

  createNewContact() {
    console.log('Someone clicked me!!!')
  }
}

```

If we run the server `ng server` and open the browser on `http://localhost:4200/` we will see our form, now open up the developer tools console and click the submit button, then we should see `Someone clicked me!!!` being outputted.

Now lets create a `contact` object of the type `any` and initialize it with some values.

```js
# src/app/app.component.ts

...

export class AppComponent {
  contact: any;

  constructor() {

    this.contact = {
      name: ``,
      email: ``,
      company: ``,
      role: ``,
      twitter: ``,
      location: ``,
      notes: ``
    }

  }

...

```

Now we need to create a two way data binding with the `contact` object and the fields in our form. The way we create two way databinding is by using Angular directive called `ngModel` and the way we write it is `[(ngModel)]="variable_name"` and we add it to a input field in the html file. 

So in the `app.component.html` file we add `[(ngModel)]="contact.name"` to the input field for name.

```html
<input [(ngModel)]="contact.name" class="appearance-none block w-full bg-grey-lighter text-grey-darker border border-grey-lighter rounded py-3 px-4 mb-3" id="name" type="text" name="name" placeholder="John Doe">
```

And do the same for all the other input fields, just remember to change `contact.name` to whatever value is in the name attribute on the input you are adding it to. When you have done that go to the browsers console and you should see the error `Can't bind to 'ngModel' since it isn't a known property of 'input'.` To fix that we need to import the `FormsModule` in the `app.module.ts` file.

First add the import to the top of the file.

```js
# src/app/app.module

import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
```

And then in the imports section we add `FormsModule`.

```js
# src/app/app.module

  imports: [
    BrowserModule,
    FormsModule
  ],
```

Go to the browser and the error should have disappeared.

As it stands when we click the submit button its only console logging a message but what we want to do is add the content of our form to an array of contacts. We start by creating a `contacts` object of the type `any[]` and set it to an empty array and we then update our `createNewContact` function pus the `contact` object into the `contacts` array and we extract the initializeation of `contact` object into a function so that we can empty it whenever we have added the information to the `contacts` array.

```js
# src/app/app.component.ts

export class AppComponent {
  contact: any
  contacts: any[] = [];

  constructor() {
    this.initContact();
  }

  createNewContact() {
    console.log(`Creating the following contact: ${JSON.stringify(this.contact)}`);
    this.contacts.push(this.contact);
    this.initContact();
  }

  private initContact() {
    this.contact = {
      name: ``,
      email: ``,
      company: ``,
      role: ``,
      twitter: ``,
      location: ``,
      notes: ``
    }
  }
}
```

Now lets add some code to the bottom of the html file to display the content of the `contacts` array

```html
# src/app/app.component.html

<pre>
  {{ contacts | json }}
</pre>

```

Now open the server `ng serve` and go to `http://localhost:4200/` and you should see a empty array below the form. Add a contact and see what happens.




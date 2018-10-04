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
# e2e/src/app.e2e-spec.ts

  navigateTo() {
    return browser.get('/');
  }
```

Now lets remove the it block that was generated for us and add these two tests instead.

```js
# e2e/src/app.e2e-spec.ts

  it('should display create contact header', () => {
    expect(page.getParagraphText()).toEqual('Create contact');
  });

  it('should add a new contact', () => {
    let count = page.cardCount();
    expect(count).toBe(0);

    page.getField('name').sendKeys('John Doe');
    page.getField('email').sendKeys('john@craftacademy.se');
    page.getField('company').sendKeys('Craft Academy');
    page.getField('role').sendKeys('Tester');
    page.getField('twitter').sendKeys('@tester');
    page.getField('location').sendKeys('Gothenburg');
    page.getField('notes').sendKeys('Some tester guy');
    
    page.getSubmitButton().click();

    count = page.cardCount();
    expect(count).toBe(1);
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

### Unit tests

As it stands when we click the submit button its only console logging a message but what we want to do is add the content of our form to an array of contacts. We start by creating a `contacts` object of the type `any[]` and set it to an empty array and we then update our `createNewContact` function to push the `contact` object into the `contacts` array and we extract the initialization of `contact` object into a function so that we can empty it whenever we have added the information to the `contacts` array.

Modify the `app.component.spec.ts` file so it looks like this.

```js
# src/app/app.component.spec.ts

import { TestBed, async } from '@angular/core/testing';
import { AppComponent } from './app.component';
import { FormsModule } from '@angular/forms';
import { Input } from '@angular/core';

describe('AppComponent', () => {

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [
        AppComponent
      ],
      imports: [
        FormsModule
      ],
    }).compileComponents();
  }));

  it('should create the app', async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app).toBeTruthy();
  }));

});
```

We use:

- `Testbed` configures and initializes environment for unit testing and provides methods for creating components and services in unit tests.

- `configureTestingModule` allows overriding default `providers`, `directives`, `pipes`, `modules` of the test injector.

- `createComponent` creates a `ComponentFixture` which is a test environment around the `component` and its `template`.

- `debugElement` gives us access to the internals of the component fixture.

- `componentInstance` will create a instance of the component we are testing.

Lets run the unit tests.

```sh
$ npm run test
```

and we should be getting this output.


![1st App component unit test](/images/1st_unit_test_app_component.png)

Lets create some more tests for the functionality we want. Add these tests after the one we already have.

```js
# src/app/app.component.spec.ts

it('createContact should add contact to contacts', async(() => {
    const contact = {
      name: 'John Doe',
      email: 'john@craftacademy.se',
      company: 'Craft Academy',
      role: 'Tester',
      twitter: '@tester',
      location: 'Stockholm',
      notes: 'There are no notes on this guy'
    }; // We create a object we can use to test
    
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    
    app.contact = contact;  // here we are passing the contact variable we created into the app.contact variable
    app.createNewContact();  // then we run the createNewContact function which will push the contact object into contacts array
    
    expect(app.contacts[app.contacts.length -1]).toEqual(contact);  // here we expect that the last object of the contacts array is the contact object
  }));

```

If you check the test status now it should give you this error.

![cannot read length of undefined](/images/length_undefined.png)

Now we can start writing the code to make this test pass. First we create the `contacts` object and set to type `any[]`^and initialize it as an empty array. Next we modify the `createNewContact` function by creating a console.log that prints out the contact object, then we use the `push` method to add the `contact` object to the `contacts` array. When we have pushed the `contact` in to the `contacts` we need to reset the `contact` values. Lets create a `private initContact` function and move the `this.contact` creation from the constructor and put it in the `initContact` function. And the we need to call that function in both the constructor and after we pushed `contact` into `contacts`.

At this point the file should look like this.

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

### Contact Component

Now we are just seeing an array of contacts but we want to make it look nice and have each contact in a card.
Lets start by creating a `contact` component that will dispay a contact as a card.

```sh
$ ng generate component card
``` 

If you take a look at `contact.component.html` file you will see a `p` tag with the text contact works!. The way can display the content that html file is calling on the component selector, check the `contact.component.ts` file and there you can see the selector. And we want to call on that selector in `app.component.html` file. Copy this to the bottom of that file instead of the `pre` tag and whats between that.

```html
# src/app/app.component.html

<app-contact></app-contact>
```

Go to to the browser and you should see below the form the text contact works!. But this will break our unit tests since `app-contact` is not a known element.

![not a known element](/images/not_a_known_ele.png)

We can fix this by adding the `ContactComponent` to the `declarations` in the `app.component.spec.ts` file.

```js
# src/app/app.component.spec.ts

import { ContactComponent } from './contact/contact.component';

describe('AppComponent', () => {

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [
        AppComponent, ContactComponent
      ],
      imports: [
        FormsModule
      ],
    }).compileComponents();
```
Check your test browser to see that you have 3 tests going green.

Add this card that we get from Tailwind documentation to `contact.component.html`.

```html
# src/app/contact/contact.component.html

<div class="card">
  <div class="image">
    <img src="#" />
  </div>
  <div class="content">
    <div class="location">Gothenburg</div>
    <h1>Nils Magnusson</h1>
    <h2>CEO, NM.com</h2>
    <p>Just some made up guy</p>
      nils@nils.com |
    <a href="https://www.twitter.com/nilsmagnusson">@nilsmagnusson</a>
  </div>
</div>

```

And we apply some styles to make that card look nice. And take special note that we apply styles specifically to each component in Angular. can you think of any reason why that is?

```html
# src/app/contact/contact.component.html

.card {
    width: 30%;
    float: left;
    margin: 6px;
    position: relative;
    z-index: 1;
    display: block;
    background: #FFFFFF;
    min-width: 270px;
    height: 450px;
    font-family: 'Mukta Vaani', sans-serif;
    color: #999999;
    -webkit-box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.15);
    -moz-box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.15);
    box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.15);
    -webkit-transition: all 0.3s linear 0s;
    -moz-transition: all 0.3s linear 0s;
    -ms-transition: all 0.3s linear 0s;
    -o-transition: all 0.3s linear 0s;
    transition: all 0.3s linear 0s;
  }
  .card:hover,
  .hover {
    -webkit-box-shadow: 0px 1px 35px 0px rgba(0, 0, 0, 0.3);
    -moz-box-shadow: 0px 1px 35px 0px rgba(0, 0, 0, 0.3);
    box-shadow: 0px 1px 35px 0px rgba(0, 0, 0, 0.3);
  }
  .card:hover .image img,
  .hover .image img {
    -webkit-transform: scale(1.1);
    -moz-transform: scale(1.1);
    transform: scale(1.1);
    opacity: .6;
  }
  .card .image {
    background: #000000;
    height: 325px;
    overflow: hidden;
  }
  .card .image img {
    display: block;
    width: 120%;
    -webkit-transition: all 0.3s linear 0s;
    -moz-transition: all 0.3s linear 0s;
    -ms-transition: all 0.3s linear 0s;
    -o-transition: all 0.3s linear 0s;
    transition: all 0.3s linear 0s;
  }
  .card .content {
    position: absolute;
    bottom: 0;
    background: #FFFFFF;
    width: 100%;
    padding: 30px 30px 20px 30px;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    -webkit-transition: all 0.3s cubic-bezier(0.37, 0.75, 0.61, 1.05) 0s;
    -moz-transition: all 0.3s cubic-bezier(0.37, 0.75, 0.61, 1.05) 0s;
    -ms-transition: all 0.3s cubic-bezier(0.37, 0.75, 0.61, 1.05) 0s;
    -o-transition: all 0.3s cubic-bezier(0.37, 0.75, 0.61, 1.05) 0s;
    transition: all 0.3s cubic-bezier(0.37, 0.75, 0.61, 1.05) 0s;
  }
  .location {
    position: absolute;
    top: -34px;
    left: 0;
    background: rgb(251, 181, 101);
    padding: 10px 15px;
    color: #FFFFFF;
    font-size: 14px;
    font-weight: 600;
    text-transform: uppercase;
  }
  h1 {
    margin: 0;
    color: #505050;
    font-size: 26px;
    font-weight: 900;
  }
  h2 {
    margin: 0;
    padding: 0 0 5px;
    color: #ff9012;
    font-size: 20px;
    font-weight: 400;
    line-height: 1.4em;
  }
  p {
    display: block;
    color: #666666;
    font-size: 14px;
    line-height: 1.5em;
  }
  a {
    color: #ff9012 !important;
    text-decoration: none;
  } 

  ```

### Display the contacts

We want to use the `contact` component to display all the contacts in the `contacts` array. Start by updating the `app.component.html` `<app-contact></app-contact>` to look like this.

```html
# src/app/app.component.html

<ng-container class="container">
  <app-contact></app-contact>
</ng-container>
```

We will use a Angular directive call `ngFor` to iterate over the `contacts` array, its a `for` loop that we can use in the `html` file.

```html
# src/app/app.component.html

<ng-container class="container" *ngFor="let contact of contacts">
  <app-contact></app-contact>
</ng-container>

```
If we view the application in our browser now we would just see the hardcoded information we set in the `contact.component.html` whenever we click the submit button. Just try clicking the button a few times.

What we want to do is send the information we fill in the form to `contact.component` so we can display our cards with the user information.  First we need to prepare the `contact` component to receive data and we will use a `decorator` called `Input` to do that.

Start by importing the `Input` from `@angular/core` and then  we need enable the `contact` variable to receive data and to do that we put `@input()` in front of the variable.

```js
# src/app/contact/contact.component.html

import { Component, OnInit, Input } from '@angular/core';

@Component({
  selector: 'app-contact',
  templateUrl: './contact.component.html',
  styleUrls: ['./contact.component.css']
})
export class ContactComponent implements OnInit {
  @Input() contact: any;

  constructor() {}

  ngOnInit() {
    this.contact = {
      name: '',
      email: '',
      company: '',
      role: '',
      twitter: '',
      location: '',
      notes: ''
    };
  }

```

Next thing we need to do is prepare the `contact.component.html` to use the `contact` variable to display the date.

```html
# src/app/contact/contact.component.html

<div class="card">
  <div class="image">
    <img src="{{ contact.image }}" />
  </div>
  <div class="content">
    <div class="location">{{ contact.location }}</div>
    <h1>{{ contact.name }}</h1>
    <h2>{{ contact.role }}, {{ contact.company }}</h2>
    <p>{{ contact.notes }}</p>
    {{ contact.email }} |
    <a href="https://www.twitter.com/{{ contact.twitter }}">@{{ contact.twitter }}</a>
  </div>
</div> 

```

Now that the `contact` component is ready to receive the data we need to send it from the `app.component` and we will use the `ngFor` loop we created to do that. Lets create a databinding between the `c` variable from the `ngFor` loop and the `contact` variable we have in the `contact.component`.

```html
# src/app/contact/contact.component.html

<ng-container class="container" *ngFor="let c of contacts">
  <app-contact [contact]="c"></app-contact>
</ng-container>

```

At this point our cards should be displaying correctly. Lets verify that everything is working correctly by running our tests. Lets start with looking at the unit tests.

![3 unit tests are passing](/images/aa_3_units_green.png)

Now lets take a look at the e2e tests.

![2 e2e tests are passing](/images/aa_2_e2e_tests_green.png)


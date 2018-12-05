---
title: "Address Book with Sails.js"
subtitle: "Address Book challenge with Sails.js"
author: [Craft Academy - Coding as a Craft]
date: Version 1.0
subject: "sails.js, javascript framework"
keywords: [sails, javascript, framework]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...


# Address Book challenge in [Sails.js](https://sailsjs.com/)

## Setup Sails.js app

First, we have to install Sails.js globally on your computer

```
npm install sails -g
```

Run ```node -v``` in the terminal to se what node version you have.
If the version is lower then 9.11.1 you need to update (```brew update node```) the node version for this setup to work.

Once this is done it is time to scaffold the application.

```
$ sails new adress_book
```

When you get the choice to choose a template for your new Sails app you should pick the empty one. So type in “2” in the terminal and press enter.

Now you need to cd into the folder.

```
$ cd adress_book
```

We need to setup the database for the application, run this in the terminal:

```
$ npm install sails-postgresql --save
```
Now we have to configure the application to know what databse to use.
The config/datastores.js should look like this:

```javascript
module.exports.datastores = {
  default: {
    adapter: "sails-postgresql",
    url: "postgresql://postgres:postgres@localhost:5432/adress_book"
  }
};
```

The application is now lokking for a database named "adress_book", but at this point we dont have a database named that. So let create one!
In the terminal run this :

```
$ createdb adress_book
```

If you open up PostgresQL on your computer you can now see that database.

## Setup Version control
To start using Version Control in this project you need to run  `git init` in the terminal.

Head over to GitHub and create a new repository called `adress_book`. Copy the new repository `https` or  `git` address (depending if you have set up your SSH keys on GitHub) and add that as a remote to your local repo.

Now lets push up the code we have scaffolded to the repo

```
$ git add .
$ git ci -m 'Scaffold sails application and setup database'
$ git push origin master
```

We dont want to work on the master branch while developing this application, so lets branch off.

```
$ git co -b development
```

## Cucumber.js
To be able to test with cucumber we need install some packages, run:

```bash
$ yarn add cucumber chai puppeteer --dev
```

If you go to your `package.json` file you sould see these packages listed in the devDependencies like this:

```json
...
"devDependencies": {
  "@sailshq/eslint": "^4.19.3",
  "chai": "^4.1.2",
  "cucumber": "^4.2.1",
  "mocha": "^5.1.1",
  "puppeteer": "^1.3.0"
},
...
```

It is time to create the nessecery files and folder structure for the acceptance tests.

```bash
$ mkdir -p features/step_definitions
$ mkdir -p features/support
$ touch features/support/world.js
$ touch features/create_contacts.feature
```

Open the ``create_contacts.feature`` and add this:

```gherkin
Feature: Create contacts
  As a user
  In order to stay in touch with my friends
  I would like to be able to create a contact for them in my 		   address book

  Scenario: Create a new contact
    Given I visit the site
    Then I should see "Contacts"
    Then I fill in "Name" with "John Doe"
    And I fill in "Email" with "john@doe.com"
    And I fill in "Phone" with "0123456789"
    And I fill in "Company" with "Craft Academy"
    And I fill in "Notes" with "A really awsome guy :-)"
    And I fill in "Twitter" with "johndoe"
    And I click "Save contact"
    And I should see "John Doe"
```
Open up the `package.json` file again and you should see a section in there which says `scripts`.  Add this there:

```json
"scripts": {
	...,
    "cucumber": "cucumber-js"
  },
```

Now you can run `yarn cucumber` and you will get a response in the terminal with the empty step defentions.

At this point we dont have a step defention file, so lets run
`$ touch features/step_definitions/basic-steps.js` in the terminal.

Copy the empty step definitions you got when you ran cucumber before into ``basic-steps.js`` and require `cucumber`.  The file should look like this:

```javascript
const  { After, Given, Then, When }  =  require('cucumber')

Given('I visit the site', function () {
			// Write code here that turns the phrase above into 					   concrete actions
			return 'pending';
});
Then('I should see {string}', function (string) {
           // Write code here that turns the phrase above into concrete actions
           return 'pending';
});

Then('I fill in {string} with {string}', function (string, string2) {
           // Write code here that turns the phrase above into concrete actions
           return 'pending';
});

Then('I click {string}', function (string) {
           // Write code here that turns the phrase above into concrete actions
           return 'pending';
});
```
## Making the first test pass

The first step in our scenario is to visit the homepage of our app. Let's update the step definition for this step as follow.

```javascript
Given('I visit the site',  async  function()  {
 return  await  this.openHomePage()
})
```
`openHomePage()` is a helper method that should navigate to the home page of the app after opening a browser.

All the helpers will be created in the `world.js` file inside the `features/support` directory. Add this code there:

```javascript
const { setWorldConstructor } = require("cucumber");
const { expect } = require("chai");
const puppeteer = require("puppeteer");

const HOME_PAGE = "http://localhost:1337";

class AdressBook {
  constructor() {}

  async openHomePage() {
    this.browser = await puppeteer.launch();
    this.page = await this.browser.newPage();

    await this.page.goto(HOME_PAGE);
  }
}

setWorldConstructor(AdressBook);
```

Okay, so lets run the tests to see if we get a green now. To run the cucumber test you first need to start the server first. You do this by running `sails lift`. Run the server in another tab in the terminal.

In the other tab run `yarn cucumber`.

Yay we get our first green!

There is one small problem. The process is still running after finishing the steps. To close it you need to press `ctrl + c`. So we are opening a page but we do not close it when it is done.
We fix that by creating another helper method in the world.js file.

```javascript
async  closeHomePage()  {
 await  this.browser.close()
}
```
And in the basic-steps.js file we add this

```javascript
After(async  function()  {
 return  await  this.closeHomePage()
})
```
Now if we run cucumber again the first step should go green and the process finish correctly.

## Add form for new contact
Next step is that we should see "Contacts" when we are at the homepage.
Make the step defenition look like this

```javascript
Then('I should see {string}',  async  function(content)  {
 return  await  this.pageHasTextContent(content)
})
```

And the ``pageHasTextContent``helper method should look like this in the `world.js` file:

```javascript
  async pageHasTextContent(expectedContent) {
    const pageContent = await this.page.content();
    const actualContent = pageContent.match(expectedContent)[0];

    expect(actualContent).to.be.eq(expectedContent);
  }
```

To make the next test pass we need to make sure that in the root path it says "Contacts". When you scaffold a sails.js application it creates a home page for you automaticly. So open up `views/pages/homepage.ejs` and remove all of the code there. After that you can add a header that says "Contacts".

```html
<body>
 <h1>Contacts</h1>
 <!-- ... -->
</body>
```

If you run the test now it should go green.

The test after this is ``Then I fill in "Name" with "John Doe"``.
So again we need to update our ``basic-steps.js`` and ``world.js``
The step defenition should look like this:

```javascript
Then('I fill in {string} with {string}', async function(field, content) {
  return await this.fillFormField(field.toLowerCase(), content);
})
```
The helper method in ``world.js``should look like this:

```javascript
async fillFormField(field, content) {
    const inputSelector = `#contact-${field}`
    await this.page.waitForSelector(inputSelector)
    this.inputElement = await this.page.$(inputSelector)
    await this.inputElement.type(content)
  }
```
If you run the cucumber tests again now it will fail at `Then I fill in "Name" with "John Doe"`.  That´s because we dont have any forms in our view. Let´s add some to our `homepage.ejs`!

```html
<body>
	<!-- ... -->
	<form  action="#">
		 <label  for="name">Name</label>
		 <input  id="contact-name"  name="name"  type="text">
		 <label  for="email">Email</label>
		 <input  id="contact-email"  name="email"  type="email">
		 <label  for="phone">Phone</label>
		 <input  id="contact-phone"  name="phone"  type="text">
		 <label  for="company">Company</label>
		 <input  id="contact-company"  name="company"  type="text">
		 <label  for="notes">Notes</label>
		 <textarea  id="contact-notes"  name="notes"  type="text"></textarea>
		 <label  for="twitter">Twitter</label>
		 <input  id="contact-twitter"  name="twitter"  type="text">
	</form>
</body>
```

When you run the test now you should get alot of new green tests!

## Contact model and controller
The next test is that the user clicks on a save button. The step defenition for this looks like this:

```javascript
When('I click {string}',  async  function(string)  {
 return  await  this.clickOnAddContactBtn()
})
```
And the helper method in `world.js` looks like this:

```javascript
async  clickOnAddContactBtn()  {
 const btnSelector =  '.save-contact'
 await  this.page.waitForSelector(btnSelector)
 await  this.page.click(btnSelector)
}
```

When you run the test again you fill get an error, that is because you dont have a button in your view. Add this to `homepage.ejs` within the form:

```html
<body>
	<form>
		<!-- ... -->
		<input class="save-contact" type="submit" value="Save contact">
	</form>
</body>
```

This will make that test go green. So this means that there is a "Save contact" button and the user can press. But the next and last test is that we should now see the name of the contact we created.

At this point we dont have a model named "Contact", so lets start with that. In the terminal run `sails generate model Contact`.

This will create a new file in `api/models` named `Contact.js`.
In here is where we set the attributes of the model. So all of the attributes that we set for the contact in the view should be set here. It should look like this:
`Contact.js`

```javascript
module.exports = {
  attributes: {
    name : { type: 'text' },
    email : { type: 'string' },
    phone : { type: 'text' },
    company : { type: 'text' },
    notes : { type: 'text' },
    twitter : { type: 'text' }
  }
};
```
Okay so now we have the model, but we also have to make the "Save contact" button to actually create a contact. So let´s start with the routes, open up `config/routes.js` and change it to look like this:

```javascript
module.exports.routes = {
  '/': 'ContactsController.index',
  'post /create': 'ContactsController.create'
};
```
As you can see we are abondoning `homepage.ejs` as our root path. Becouse of what we are going to access from our root path we need to have a controller for it.  The problem now is that we dont have a contacts controller. Run this in the terminal `touch api/controllers/ContactsController.js`

Add this code to the `ContactsController.js`:

```javascript
module.exports = {
  create: function(req,res) {
  	var params = _.extend(req.query || {}, req.params || {}, req.body || {});

  	Contact.create(params, function userCreated (err, createdContact) {

  		if (err) return res.send(err,500);

  	});
    Contact.find().exec(function(err, contacts) {
                if(err) throw err;
                res.redirect('/');
    });
  },

  index: function(req, res) {
    Contact.find().exec(function(err, contacts) {
                if(err) throw err;
                res.view('contacts/index', {contacts: contacts});
    });
  }
}
```

So now we want to create the index page for contacts.
Run `mkdir views/contacts` in the terminal and after that run `touch views/contacts/index.html`.

So the code we added to `homepage.ejs` before can now be added to this file. We will also add a loop that will show all the contacts that has been created. This part is what will make the final test to pass. We will also call on the create action in the form so that when the user clicks the "Save contact" button it creates a contact.

The `index.js` file should look this:

```html
<h1>Contacts</h1>

<table>
  <% contacts.forEach(function(contact){ %>
    <tr>
      <td><%= contact.name %></td>
      <td><%= contact.email %></td>
      <td><%= contact.phone %></td>
      <td><%= contact.company %></td>
      <td><%= contact.notes %></td>
      <td><%= contact.twitter %></td>
    </tr>
  <% }); %>
</table>
<form action="/create" method="post">
  <label for="name">Name</label>
  <input id="contact-name" name="name" type="text">
  <label for="email">Email</label>
  <input id="contact-email" name="email" type="string">
  <label for="phone">Phone</label>
  <input id="contact-phone" name="phone" type="text">
  <label for="company">Company</label>
  <input id="contact-company" name="company" type="text">
  <label for="notes">Notes</label>
  <textarea id="contact-notes" name="notes" type="text"></textarea>
  <label for="twitter">Twitter</label>
  <input id="contact-twitter" name="twitter" type="text">
  <input class="save-contact" type="submit" value="Save contact">
</form>
```

If you run the test now you will probably get an error saying `Error: Protocol error (Runtime.callFunctionOn): Cannot find context with specified id undefined`. This is because the page does not have time to display the created contact when cucumber is looking for it. So we need to add a sleep function, so the test takes a quick break so the page can have time to display the contact before cucumber checks for it. So in `world.js` you need to add:

```javascript
async sleep(milliseconds) {
  var start = new Date().getTime();
  for (var i = 0; i < 1e7; i++) {
    if ((new Date().getTime() - start) > milliseconds){
      break;
    }
  }
}
```
Now in `basic-steps.js` we need to call on it in the step definition that is checking for content on the page, it should look like this:

```javascript
Then('I should see {string}',  async  function(content)  {
  return await this.sleep(3000);
  return  await  this.pageHasTextContent(content)
})
```
**Now all the tests should go green!!!**
In this section, we will do a bit of refactoring to allow our application to save multiple contacts. In order to expose that flaw we should write a new scenario where the user tries to save more than one contact. Add the following scenario to your feature file and let's get started.

```gherkin
Feature: Create contacts
  As a user
  ...
  
  Scenario: Create a new contact
    ...
  
  Scenario: User creates multiple contacts
    Given I visit the site
    Then I should see "Contacts"
    And I should see "You have no contacts in your address book"
    When I click "Add contact"
    Then I fill in "Name" with "John Doe"
    And I fill in "Email" with "john@doe.com"
    And I fill in "Phone" with "0123456789"
    And I fill in "Company" with "Craft Academy"
    And I fill in "Notes" with "A really awsome guy :-)"
    And I fill in "Twitter" with "johndoe"
    And I click "Save contact"
    And I click "Add contact"
    Then I fill in "Name" with "Jane Doe"
    And I fill in "Email" with "jane@doe.com"
    And I fill in "Phone" with "01111111111"
    And I fill in "Company" with "Craft Academy"
    And I fill in "Notes" with "A really awsome girl :-)"
    And I fill in "Twitter" with "janedoe"
    And I click "Save contact"
    Then I should have 2 contact in my address book
    And I should see "John Doe"
    And I should see "Jane Doe"
    And I should not see "You have no contacts in your address book"

```

Running cucumber now will yield the following output.

![Test fails - expected 2 contacts only got 1](https://github.com/CraftAcademy/ca-course-week-2/raw/master/.gitbook/assets/screenshot-2018-06-18-09.09.45.png)


As you can see from the error message, we expected to have 2 contacts, but we only have 1. But what could possibly be wrong with our current implementation? 

In your `app.js` file, the following lines are responsible for saving contacts to our storage.

```javascript
storage.setItem('contacts', JSON.stringify([contact]))
```

When we create the `contact` object from the form inputs, we call on the `setItem` function of LocalStorage to create a contacts items with the content of a stringified version of an array containing that single `contact` object.

What we really want to do here is:

1. Read our storage to get any contact that might be currently stored
2. Add the new contact to the array of previous contacts
3. Save the updated array of contacts

```javascript
...

// 1. Read existing contacts from storage
// Note that we assign an empty array in case we have nothing stored at the moment
let contacts = JSON.parse(storage.getItem('contacts')) || []

// 2. Add the new contact to the contacts array
contacts.push(contact)

// 3. Save the new list
storage.setItem('contacts', JSON.stringify(contacts))
```

Now, if we run our tests again, every scenario we have described should pass.
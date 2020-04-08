In the previous section, we started writing some cucumber steps for creating a contact. Open `main.feature` and update it with the following code.

```text
Feature: Create contacts
  As a user
  In order to stay in touch with my friends
  I would like to be able to create a contact for them in my address book


  Scenario: Create a new contact
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
    Then I should have 1 contact in my address book
    And I should see "John Doe"
    And I should not see "You have no contacts in your address book"
```

Also update your `basic-step.js` to the following:

```javascript
const { Given, Then, When } = require('cucumber')

Given('I visit the site', async function() {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

Then('I should see {string}', async function(content) {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

When('I click {string}', async function(string) {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

Then('I fill in {string} with {string}', async function(string, string2) {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

Then('I should have {int} contact in my address book', async function(int) {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

Then('I should not see {string}', async function(string) {
  // Write code here that turns the phrase above into concrete actions
  return 'pending'
})

```

Great! Now that this is out of the way let's fill up those steps with some code to get our tests to go green.

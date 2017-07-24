## Exercise - Implement extra features

Now that you've been introduced to the AUT cycle within the context of a Rails application, we've prepared a few extra users stories for you to work on.

* #### Create Article Feature

```
As a blogger
In order to share my content with blog visitors
I would like to be able to create articles
```

Here is a starting feature file for the create functionality.

```
Feature: Create articles
  As a blogger
  In order to share my content with everyone
  I would like to be able to create Articles

  Background:
    Given I visit the "landing" page
    When I click "new article" link

  Scenario: Successfully create an article [Happy Path]
    When I fill in "Title" with "Learning Rails 5"
    And I fill in "Content" with "Excited about learning a new framework"
    And I click "Create Article" button
    Then I should be on "Learning Rails 5" page
    And I should see "Article was successfully created."
    And I should see "Learning Rails 5"
    And I should see "Excited about learning a new framework"

  Scenario: Blogger doesn't enter a title for the article [Sad Path]
    When I fill in "Content" with "Excited about learning a new framework"
    And I click "Create Article" button
    Then I should see "Title can't be blank"

  # Write another sad path scenario you can think of
```

* #### Edit \(Update\) Article Feature

```
As a blogger
In order to keep my content accurate
I would like to be able to edit my articles
```



The purpose of this assignment is to get you familiar with how routes, controllers, and views interact with each others within the context  of a rails application. As such the solution to the create functionality is can be found here in the [Rails official guides](http://guides.rubyonrails.org/getting_started.html). But you need to let your tests guide you with what to do next, this is a very important aspect of the exercise. Throughout the week, we'll be discussing these concepts in details.




Now that you've been introduced to the AUT cycle within the context of a Rails application, we've prepared a few extra user stories for you to work on.

### CREATE ARTICLE FEATURE

    As a publisher
    In order to add relevant content to my news service
    I would like to be able to create articles
    Here is a starting feature file for the create functionality.

    Feature: Create articles
      As a publisher
      In order to add relevant content to my news service
      I would like to be able to create articles

      Background:
        Given I visit the "landing" page
        And I click "New Article" link

      Scenario: Successfully create an article [Happy Path]
        When I fill in "Title" with "Learning Rails 5"
        And I fill in "Content" with "Excited about learning a new framework"
        And I click "Create Article" button
        Then I should be on "Learning Rails 5" page
        And I should see "Article was successfully created."
        And I should see "Learning Rails 5"
        And I should see "Excited about learning a new framework"

      Scenario: Publisher doesn't enter a title for the article [Sad Path]
        When I fill in "Content" with "Excited about learning a new framework"
        And I click "Create Article" button
        Then I should see "Title can't be blank"

      # Write another sad path scenario you can think of
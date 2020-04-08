Let's develop a simple feature. In the scenario, a user will visit the application's landing page and see a list of news articles displayed.

Let's start by creating a feature file.

`features/user_can_see_list_of_articles.feature`

    Feature: List articles on landing page
      As a visitor, 
      when I visit the application's landing page,
      I would like to see a list of articles

      Scenario: Viewing list of articles on the application's landing page
        When I am on the index page
        Then I should see "A breaking news item"
        And I should see "Some breaking action"

Run the scenario and see it fail.

    $ bundle exec cucumber features/user_can_see_list_of_articles.feature

In the terminal output, you will see snippets for implementing steps:

    You can implement step definitions for undefined steps with these snippets:

    When('I am on the index page') do
      pending # Write code here that turns the phrase above into concrete actions
    end

    Then("I should see {string}") do |content|
      pending # Write code here that turns the phrase above into concrete actions
    end

Let's copy those steps into `features/step_definitions/landing_page_steps.rb` and edit them:

    When('I am on the index page') do
      visit root_path
    end

    Then("I should see {string}") do |content|
      expect(page).to have_text content
    end

If you run the scenario again, you will see that it fails since the route `root_path` is not defined. Let's add it to our `routes.rb` file:

`config/routes.rb`

    Rails.application.routes.draw do
      root controller: :landing, action: :index
    end

Now implement the controller that will serve the `/` route. For that, we will use a Rails Generator.

    $ rails generate controller landing index
          create app/controllers/landing_controller.rb
          route get 'landing/index'
          invoke erb
          create app/views/landing
          create app/views/landing/index.html.erb
          invoke rspec

Now that we have the `root_path` setup with the LandingController, let's run our tests again. The first step of our scenario is now green.

    Using the default profile...
    [...]

    Scenario: Viewing list of articles on the application's landing page # features/list_articles.feature:6
      When I am on the landing page # features/step_definitions/landing_page_steps.rb:1
      Then I should see "A breaking news item" # features/step_definitions/landing_page_steps.rb:5
        expected to find text "A breaking news item" in "Landing#index Find me in app/views/landing/index.html.erb" (RSpec::Expectations::ExpectationNotMetError)
        ./features/step_definitions/landing_page_steps.rb:6:in `/^I should see "([^"]*)"$/'
        features/list_articles.feature:8:in `Then I should see "A breaking news item"'
      And I should see "Some really breaking action" # features/step_definitions/landing_page_steps.rb:5

    Failing Scenarios:
    cucumber features/list_articles.feature:6 # Scenario: Viewing list of articles on the application's landing page

    1 scenario (1 failed)
    3 steps (1 failed, 1 skipped, 1 passed)
    0m0.533s

Let's get the remaining steps to a green state too.
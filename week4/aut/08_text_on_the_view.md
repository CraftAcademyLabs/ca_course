Looking at the previous output of cucumber, we note that it expected to see some text on the page, but didn't find any that was specified. It also points us to the file that was supposed to render the text in question `app/views/landing/index.html.erb`.

All we need to do at this stage to get the remaining tests to pass is add the respective texts to that view file. Edit `app/views/landing/index.html.erb` and replace the file's content with:

    <p>A breaking news item</p>
    <p>Some really breaking action</p>

Run cucumber now and see all the tests pass.

Great! all our steps are now green. We are not quite done yet though. If we take a look at our user story:

    As a visitor, 
    when I visit the application's landing page,
    I would like to see a list of articles

We want to list several articles on our landing page. All we managed to do so far is display two static texts on our page. Let's update our cucumber scenario to reflect that:

    Feature: List articles on landing page
      As a visitor,
      when I visit the application's landing page,
      I would like to see a list of articles

      Background: 
        Given the following articles exist:
        | title                | content                            |
        | A breaking news item | Some breaking action        |
        | Learn Rails 5        | Build awesome rails applications   |

      Scenario: Viewing list of articles on the application's landing page
        When I am on the index page
        Then I should see "A breaking news item"
        And I should see "Some breaking action"
        And I should see "Learn Rails 5"
        And I should see "Build awesome rails applications"

We now have a `Background` block that will set the stage for our scenario by creating two articles in our database. We then check to see that both articles are displayed on our landing page. If we run cucumber now, we get an undefined step with the following code snippet:

    Given("the following articles exist:") do |table|
      # table is a Cucumber::MultilineArgument::DataTable
      pending # Write code here that turns the phrase above into concrete actions
    end

Let's add that to our step definition.

    Given("the following articles exist:") do |table|
      table.hashes.each do |hash|
        Article.create!(hash)
      end
    end

Now run the feature file and see a new error message:

    ...

    uninitialized constant Article (NameError) 
    ...

    Failing Scenarios:
    cucumber features/list_articles.feature:12 # Scenario: Viewing list of articles on the application's landing page

    1 scenario (1 failed)
    6 steps (1 failed, 5 skipped)
    0m0.155s

**This is a very crucial step in our development process.** This is as far as you can go in the high-level tests - at this point, we need to shift our attention to the process of creating our units (Article) using tests as a blueprint. It's time to focus on the **lower-level** (inside) tests using RSpec.
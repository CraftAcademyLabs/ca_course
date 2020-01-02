At this point, we can go back to our acceptance test and run Cucumber again. We are still not in the green, but we should have a different error message.

Now, we need to modify the controller as well as the view to display (or try to display) our articles.

    class LandingController < ApplicationController
      def index
        @articles = Article.all
      end
    end

And replace the content of the view that will render `LandingController#index` action and list all posts.

    <ul>
      <% @articles.each do |article| %>
        <li>
          <%= article.title %><br />
          <%= article.content %>
        </li>
      <% end %>
    </ul>

At this point, if you run `cucumber` you should see all tests passing green.

#### Wrap up

During this walkthrough, we have completed one Acceptance-Unit Cycle (without the refactoring part) and added a simple feature that allows visitors to view articles on the landing page of the application.

Using our tests we were able to craft out some functionality and delivered the objective of the feature: To list articles on the landing page.

*   We created a route (URL)
*   We created an Article model with attributes and validations
*   We created a controller with an index method that fetches all articles from the database and stores the collection in a variable that is made available to the view template
*   We created a view that iterates through a collection of articles

In the next cycle, we would certainly add more scenarios to this feature to test other paths. What should happen when there are no articles in the system? Will multiple articles be displayed correctly? Etc..

**It takes a little practice but with this approach, you are constantly in charge of the workflow and know what the next step of your implementation should be.**
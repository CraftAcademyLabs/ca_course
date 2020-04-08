## Back to acceptance tests

At this point, we can go back to our acceptance test and run `rspec spec/features/user_can_see_list_of_articles_spec.rb` again. We are still not in the green, but we should have a different error message.

Now, we need to modify the controller as well as the view to display (or try to display) our articles.

```rb
# app/controllers/articles_controller.rb

class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

And replace the content of the view that will render ArticlesController#index action and list all posts.

```html
<%# app/views/articles/index.html.erb %>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= article.title %><br />
      <%= article.content %>
    </li>
  <% end %>
</ul>
```

At this point, if you run `rspec` you should see all tests passing green.
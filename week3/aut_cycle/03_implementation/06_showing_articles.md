The user can now successfully see a list of all the articles that the application has in the databse. The next step is that a user can click on one of the articles being listed and then read the whole article.

Create a new feature file.
`$ touch spec/features/user_can_see_specific_article_spec.rb`


```rb
# spec/features/user_can_see_specific_article_spec.rb

feature 'User can see specific article' do
  before do
    create(:article, title: 'A breaking news item', content: 'Some breaking action')
    create(:article, title: 'Learn Rails 5', content: 'Build awesome rails applications')

    visit root_path
    click_on 'A breaking news item'
  end

  context 'Article displays' do
    it 'title' do
      expect(page).to have_content 'A breaking news item'
    end

    it 'content' do
      expect(page).to have_content 'Some breaking action'
    end
  end
end
```

When you run this feature in the terminal (`$ rspec spec/features/user_can_see_specific_article_spec.rb`) you should get this error message.

```bash
1) User can see specific article Article displays title
     Failure/Error: create(:article, title: 'A breaking news item', content: 'Some breaking action')
     
     NoMethodError:
       undefined method `content=' for #<Article:0x0000565507b5ba28>
```

At this point our `Article` model only has one attribute, `:title`. When the user is going to read the article we want to display the content of that article. Since we dont have a `:content` attribute for the article model we cant create one with it.

So we need to add the `:content` attribute. Firts we want to update the exsisting unit spec for the `Àrticle` model. 

```rb
# spec/models/article_spec.rb

RSpec.describe Article, type: :model do
  describe 'DB table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :title }
    it { is_expected.to have_db_column :content }
  end

  describe 'Validations' do
    it { is_expected.to validate_presence_of :title }
    it { is_expected.to validate_presence_of :content } 
  end

  describe 'Factory' do
    it 'should have valid Factory' do
      expect(create(:article)).to be_valid 
    end
  end
end
```

If you run the unit specs now (`rspec spec/models`), you will get this error message.

```bash
1) Article DB table is expected to have db column named content
     Failure/Error: it { is_expected.to have_db_column :content }
       Expected Article to have db column named content (Article does not have a db column named content.)
     # ./spec/models/article_spec.rb:5:in `block (3 levels) in <top (required)>'

  2) Article Validations is expected to validate that :content cannot be empty/falsy
     Failure/Error: it { is_expected.to validate_presence_of :content }
     
     Shoulda::Matchers::ActiveModel::AllowValueMatcher::AttributeDoesNotExistError:
       The matcher attempted to set :content on the Article to nil, but that
       attribute does not exist.
```

It is time to create a migration to add the `:content` column to `Article`.

`$ rails g migration AddContentToArticle content:text`

```rb
class AddContentToArticle < ActiveRecord::Migration[6.0]
  def change
    add_column :articles, :content, :text
  end
end
```

In order to add this column now we need to migrate the database.
`$ rails db:migrate`

If you run the unit test again you will have a new error message.

```bash
1) Article Validations is expected to validate that :content cannot be empty/falsy
     Failure/Error: it { is_expected.to validate_presence_of :content }
     
       Expected Article to validate that :content cannot be empty/falsy, but
       this could not be proved.
         After setting :content to ‹nil›, the matcher expected the Article to
         be invalid, but it was valid instead.
```

At the moment the only thing we validate the presence of in the `Article` model is `:title`.
We need to add `:content`.
```rb
class Article < ApplicationRecord
  validates_presence_of :title, :content
end
```

We now have a new error message when we run the unit test.
```bash
1) Article Factory should have valid Factory
     Failure/Error: expect(FactoryBot.create(:article)).to be_valid
     
     ActiveRecord::RecordInvalid:
       Validation failed: Content can't be blank
```

The `FactoryBot` for `Article` tries to create an instance without adding `:content`. We need to add `:content` to the defenition of the `Àrticle` factory.
```rb
FactoryBot.define do
  factory :article do
    title { "MyString" }
    content { "MyText" }
  end
end
```

You should now get all green when you run the unit test for the `Article` model.
When you run the feature test now you should have a new error message, the articles have been successfully created in the background step. 

```bash
Failure/Error: click_on 'A breaking news item'
     
     Capybara::ElementNotFound:
       Unable to find link or button "A breaking news item"
```

We expect that the user can click on the title of a article and that will lead to that specific article. We need to make the article title to a link when we display the article index. 

```html
<%# app/views/articles/index.html.erb %>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= link_to article.title %><br />
    </li>
  <% end %>
</ul>
```

We get a new error message when we run the feature test.
```bash
User can see specific article Article displays content
     Failure/Error: expect(page).to have_content 'Some breaking action'
       expected to find text "Some breaking action" in "A breaking news item Learn Rails 5"
     # ./spec/features/user_can_see_specific_article_spec.rb:16:in `block (3 levels) in <top (required)>'
```

The link that we added leads nowhere at the moment. We need to add the path that the link will lead to.

```html
<%# app/views/articles/index.html.erb %>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= link_to article.title, article_path(article) %><br />
    </li>
  <% end %>
</ul>
```

We get a new error message when we run the feature test.
```bash
User can see specific article Article displays title
     Failure/Error: <%= link_to article.title, article_path(article) %><br />
     
     ActionView::Template::Error:
       undefined method `article_path' for #<#<Class:0x000055c7d86f31f0>:0x000055c7d8921210>
```

The link leads to the show page for that specific article, but we have not defined the route for that yet. 

```rb
Rails.application.routes.draw do
  root controller: :articles, action: :index
  resources :articles, only: [:index, :show]
end
```

We get a new error message when we run the feature test.
```bash
1) User can see specific article Article displays title
     Failure/Error: click_on 'A breaking news item'
     
     AbstractController::ActionNotFound:
       The action 'show' could not be found for ArticlesController
```

Now it complains about the `show` action not being defined in the controller. 

```rb
# app/controllers/articles_controller.rb

class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    
  end
end
```

We get a new error message when we run the feature test.
```bash
1) User can see specific article Article displays title
     Failure/Error: click_on 'A breaking news item'
     
     ActionController::MissingExactTemplate:
       ArticlesController#show is missing a template for request formats: text/html
```

The test now complains about there being no template for the show action. Lets create that!
`$ app/views/articles/show.html.erb`

We can add this to it to make sure that the test goes green.

```html
<%# app/views/articles/show.html.erb %>

<p>A breaking news item</p>
<p>Some breaking action</p>
```

This is what we call hard coded. Ff we where to change the article attributes in the before block where we create the articles for the fetaure test, we would not go green.

So what we want to do is that we want to grab the `id` of the article from the params and find the specific article and store it in a instance variabel.

```rb
# app/controllers/articles_controller.rb

class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end
end
```

Then we want to display the data we have in that variabel on the `show` page. 

```html
<%# app/views/articles/show.html.erb %>

<p><%= @article.title %></p>
<p><%= @article.content %></p>
```

If you run all tests now they should go green!

## Wrap up

During this walkthrough, we have completed one Acceptance-Unit Cycle (without the refactoring part) and added a simple feature that allows visitors to view articles on the landing page of the application.

Using our tests we were able to craft out some functionality and delivered the objective of the feature: To list articles on the landing page.

- We created a route (URL)
- We created an Article model with attributes and validations
- We created a controller with an index method that fetches all articles from the
  database and stores the collection in a variable that is made available to the view template
- We created a view that iterates through a collection of articles

In the next cycle, we would certainly add more scenarios to this feature to test other paths. What should happen when there are no articles in the system? Will multiple articles be displayed correctly? Etc…

**It takes a little practice but with this approach, you are constantly in charge of the workflow and know what the next step of your implementation should be.**
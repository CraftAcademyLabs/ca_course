
[comment]: <> (We need to have capybara gem and set up FactoryBot syntax in rails_helper 
`config.include FactoryBot::Syntax::Methods`)
## Our first AUT cycle

Let's develop a simple feature. In the scenario, a user will visit the application's landing page and see a list of news articles displayed.

Let's start by creating a high-level test file.
```rb
# spec/features/user_can_see_list_of_articles_spec.rb

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      visit root_path
    end

    it 'displays first article title' do
      expect(page).to have_content 'A breaking news item'
    end

    it 'displays second article title' do
      expect(page).to have_content 'Some really breaking action'
    end
  end
end
```

Make sure that you have created a database before you run your test.
`rails db:create db:migrate`

Run the scenario and see it fail.
`$ rspec spec/features/user_can_see_list_of_articles_spec.rb`

You will see that it fails since the route root_path is not defined. Let's add it to our `config/routes.rb` file:

```
Rails.application.routes.draw do
  root controller: :articles, action: :index
end
```
**Explain routes here**

Now implement the controller that will serve the `/` route. For that, we will use a Rails Generator.
```bash
$ rails generate controller Articles index
      create app/controllers/articles_controller.rb
      route get 'articles/index'
      invoke erb
      create app/views/articles
      create app/views/articles/index.html.erb
      invoke rspec
```
**What did we just generate?**

Now that we have the root_path setup with the ArticlesController, let's run our tests again. We now have a new error.

```
1) List articles on index page with articles in db displays first article title
     Failure/Error: expect(page).to have_content 'A breaking news item'
       expected to find text "A breaking news item" in ""
     # ./spec/features/user_can_see_list_of_articles_spec.rb:12:in `block (4 levels) in <top (required)>'

2) List articles on index page with articles in db displays second article title
    Failure/Error: expect(page).to have_content 'Some really breaking action'
      expected to find text "Some really breaking action" in ""
    # ./spec/features/user_can_see_list_of_articles_spec.rb:17:in `block (4 levels) in <top (required)>'
```

## Text on the view
Looking at the previous output of RSpec, we note that it expected to see some text on the page, but didn't find any that was specified. It also points us to the file that was supposed to render the text in question `app/views/articles/index.html.erb`.

All we need to do at this stage to get the remaining tests to pass is add the respective texts to that view file. Edit `app/views/articles/index.html.erb` and replace the file's content with:

```html
<p>A breaking news item</p>
<p>Some really breaking action</p>
```

Run RSpec now and see all the tests pass.

Great! All our steps are now green. We are not quite done yet though. If we take a look at our user story:

[comment]: <> (We do not have the user story in the feature file with RSpec, how do we want to use them?)
```
As a visitor, 
when I visit the application's landing page,
I would like to see a list of articles
```

We want to list several articles on our landing page. All we managed to do so far is display two static texts on our page. Let's update our RSpec feature test to reflect that:

```rb
# spec/features/user_can_see_list_of_articles_spec.rb

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      create(:article, title: 'A breaking news item')
      create(:article, title: 'Learn Rails 5')

      visit root_path
    end

    it 'displays first article title' do
      expect(page).to have_content 'A breaking news item'
    end

    it 'displays second article title' do
      expect(page).to have_content 'Some really breaking action'
    end
  end
end
```
**Explain changes to the feature file. `create`**


We now have a `before` block that will set the stage for our scenario by creating two articles in our database. We then check to see that both articles are displayed on our landing page. If we run RSpec now, we get an error about how `article` is not a registred factory.

```
KeyError:
       Factory not registered: "article"
```

**This is a very crucial step in our development process.** This is as far as you can go in the high-level tests - at this point, we need to shift our attention to the process of creating our units (Article) using tests as a blueprint. It's time to focus on the **lower-level** (inside) tests using RSpec.

## Unit test

Let's think about how we want our Articles to be structured:

*   We need to be able to store Articles in our database - meaning we need to create a Model
*   We want each article to have a `title` - we need to add this attributes to our Article model
*   We don't want to store articles that don't have a `title` present - meaning we need to add a validation that this attribute is not empty
*   We want to make sure that there is a valid Factory to use in our test environment

The first thing we want to do is to create a spec file in the `spec/models` folder called `article_spec.rb`. Let's add some tests (we will use the matchers provided to us by the `shoulda-matchers` gem that extends the built-in RSpec matchers).

Run these commands in the terminal:

```bash
$ mkdir spec/models
$ touch spec/models/article_spec.rb
```

Add the following code to file that we just created:
```rb
# spec/models/article_spec.rb

RSpec.describe Article, type: :model do
  describe 'DB table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :title }
  end

  describe 'Validations' do
    it { is_expected.to validate_presence_of :title }
  end

  describe 'Factory' do
    it 'should have valid Factory' do
      expect(FactoryBot.create(:article)).to be_valid 
    end
  end
end
```
If you run RSpec (and you should) now you will see the tests fail - we haven't created the **model** yet.

## The model
We'll make use of another rails generator to create the Article model. That will create the class, a migration, and a factory (if you are prompted to overwrite the `article_spec`, just type `n`)

`$ rails generate model Article title:string`

**What did we gen when generated that model?**

Following that, you need to run the `rails db:migrate` command to update your database.

```bash
$ rails db:migrate
    == 20160725233007 CreateArticles: migrating ===================================
    -- create_table(:articles)
       -> 0.0295s
    == 20160725233007 CreateArticles: migrated (0.0296s) ==========================
```
Running the spec now you will see that we are moving in the right direction but there are still some failing tests - we have not set up any validations and those assertions are giving us errors.

Open the model file and add validation for the presence of `title`.

**Why?**
```
# app/models/article.rb

class Article < ApplicationRecord
  validates_presence_of :title
end
```

Running your unit specs now (`rspec spec/models/article_spec.rb`) will result in all green tests.

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

```erb
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

#### Wrap up

During this walkthrough, we have completed one Acceptance-Unit Cycle (without the refactoring part) and added a simple feature that allows visitors to view articles on the landing page of the application.

Using our tests we were able to craft out some functionality and delivered the objective of the feature: To list articles on the landing page.

*   We created a route (URL)
*   We created an Article model with attributes and validations
*   We created a controller with an index method that fetches all articles from the database and stores the collection in a variable that is made available to the view template
*   We created a view that iterates through a collection of articles

In the next cycle, we would certainly add more scenarios to this feature to test other paths. What should happen when there are no articles in the system? Will multiple articles be displayed correctly? Etc..

**It takes a little practice but with this approach, you are constantly in charge of the workflow and know what the next step of your implementation should be.**

## More features
Now that you've been introduced to the AUT cycle within the context of a Rails application, we've prepared a few extra user stories for you to work on.

```rb
feature 'User can create articles' do
  before do
    visit root_path
    click_on "New Article"
  end
  
  context "Successfully create an article [Happy Path]" do
    before do
      fill_in "Title", with: "Happy holidays"
      fill_in "Content", with: "Buy your gifts now!"
      click_on "Create Article"
    end

    it 'User should be on article show page' do
      article = Article.find_by(title: 'Happy holidays')
      expect(current_path).to eq article_path(article)
    end

    it 'User should see success message' do
      expect(page).to have_content 'Article was successfully created.'
    end

    it 'User should see article title' do
      expect(page).to have_content 'Happy holidays'
    end

    it 'User should see article content' do
      expect(page).to have_content 'Buy your gifts now!'
    end
  end

  context "User doesn't enter a title for the article [Sad Path]" do
    before do
      fill_in "Content", with: "Buy your gifts now!"
      click_on "Create Article"
    end

    it 'User should see error message' do
      expect(page).to have_content "Title can't be blank"
    end
  end

  # Write another sad path scenario you can think of
end
```
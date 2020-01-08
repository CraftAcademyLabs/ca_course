We need to have capybara gem and set up FactoryBot syntax in rails_helper 
`config.include FactoryBot::Syntax::Methods
`

First create feature file
```rb
# spec/features/user_can_see_list_of_articles_spec.rb

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      visit root_path
    end

    it 'displays first article' do
      expect(page).to have_content 'A breaking news item'
    end

    it 'displays second article' do
      expect(page).to have_content 'Some really breaking action'
    end
  end
end
```

**Explain test here**

Run test (`rspec`)
We need to run `rails db:create db:migrate`

Run test again
```
undefined local variable or method `root_path'
```

We need a controller

`rails g controller Articles index`

**What did we just generate?**

Go to `config/routes.rb`

File should look like this:

```
Rails.application.routes.draw do
  root controller: :articles, action: :index
end
```

**Explain routes here**

Run test again

```
Failure/Error: expect(page).to have_content 'A breaking News Article'
       expected to find text "A breaking News Article" in "Articles#index\nFind me in app/views/articles/index.html.erb"
```

Add this to `app/views/articles/index.html.erb`

**Wow cool everything is going green. Why is that? What are we gonna do now? Why are we changing up the test**

Update test to look like this:

```rb
# spec/features/user_can_see_list_of_articles_spec.rb

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      create(:article, title: 'A breaking news item', content: 'Some breaking action')
      create(:article, title: 'Learn Rails 5', content: 'Build awesome rails applications')

      visit root_path
    end

    context 'displays first article' do
      it 'title' do
        expect(page).to have_content 'A breaking news item'

      end

      it 'content' do
        expect(page).to have_content 'Some breaking action'
      end
    end

    context 'displays second article' do
      it 'title' do
        expect(page).to have_content 'Learn Rails 5'

      end

      it 'content' do
        expect(page).to have_content 'Build awesome rails applications'
      end
    end
  end
end
```

**Explain changes to the feature file. `create` and `context`**

Run test

```
KeyError:
       Factory not registered: "article"
```

**This is a very crucial step in our development process.** This is as far as you can go in the high-level tests - at this point, we need to shift our attention to the process of creating our units (Article) using tests as a blueprint. It's time to focus on the **lower-level** (inside) tests using RSpec.



## Unit test

**What do we need?**

create spec file
`mkdir spec/models`
`touch spec/models/article_spec.rb`

```rb
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
      expect(FactoryBot.create(:article)).to be_valid 
    end
  end
end
```

**If you run RSpec (and you should) now you will see the tests fail - we haven't created the model yet.**

## The model

`$ rails generate model Article title:string content:text
`

**What did we gen when generated that model?**

Now run `rails db:migrate`

If you only run the unit spec
`rspec spec/models/article_spec.rb`

Everything goes green except validations

The `app/models/article.rb` should look like this:
```
class Article < ApplicationRecord
  validates_presence_of :title, :content
end
```

If you run ur test u will get all green now

## Back to acceptance tests

modify `app/controllers/articles_controller.rb` to look like this:
```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

now modify `app/views/articles/index.html.erb`
```erb
<ul>
  <% @articles.each do |article| %>
    <li>
      <%= article.title %><br />
      <%= article.content %>
    </li>
  <% end %>
</ul>
```

If you run ur tests now everything should go green!

**Wrap up!**

## More features

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
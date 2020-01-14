
```rb
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

```bash
1) User can see specific article Article displays title
     Failure/Error: create(:article, title: 'A breaking news item', content: 'Some breaking action')
     
     NoMethodError:
       undefined method `content=' for #<Article:0x0000565507b5ba28>
```

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

rspec spec/models

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

rails g migration AddContentToArticle content:text

```rb
class AddContentToArticle < ActiveRecord::Migration[6.0]
  def change
    add_column :articles, :content, :text
  end
end
```
rails db:migrate
rspec spec/models

```bash
1) Article Validations is expected to validate that :content cannot be empty/falsy
     Failure/Error: it { is_expected.to validate_presence_of :content }
     
       Expected Article to validate that :content cannot be empty/falsy, but
       this could not be proved.
         After setting :content to ‹nil›, the matcher expected the Article to
         be invalid, but it was valid instead.
```

```rb
class Article < ApplicationRecord
  validates_presence_of :title, :content
end
```

```bash
1) Article Factory should have valid Factory
     Failure/Error: expect(FactoryBot.create(:article)).to be_valid
     
     ActiveRecord::RecordInvalid:
       Validation failed: Content can't be blank
```

```rb
FactoryBot.define do
  factory :article do
    title { "MyString" }
    content { "MyText" }
  end
end
```

```bash
Failure/Error: click_on 'A breaking news item'
     
     Capybara::ElementNotFound:
       Unable to find link or button "A breaking news item"
```

```html
<ul>
  <% @articles.each do |article| %>
    <li>
      <%= link_to article.title %><br />
    </li>
  <% end %>
</ul>
```

```bash
User can see specific article Article displays content
     Failure/Error: expect(page).to have_content 'Some breaking action'
       expected to find text "Some breaking action" in "A breaking news item Learn Rails 5"
     # ./spec/features/user_can_see_specific_article_spec.rb:16:in `block (3 levels) in <top (required)>'

```

Not on show page

```html
<ul>
  <% @articles.each do |article| %>
    <li>
      <%= link_to article.title, article_path(article) %><br />
    </li>
  <% end %>
</ul>
```

```bash
User can see specific article Article displays title
     Failure/Error: <%= link_to article.title, article_path(article) %><br />
     
     ActionView::Template::Error:
       undefined method `article_path' for #<#<Class:0x000055c7d86f31f0>:0x000055c7d8921210>
```

```rb
Rails.application.routes.draw do
  root controller: :articles, action: :index
  resources :articles, only: [:index, :show]
end
```

```bash
1) User can see specific article Article displays title
     Failure/Error: click_on 'A breaking news item'
     
     AbstractController::ActionNotFound:
       The action 'show' could not be found for ArticlesController
```

```rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    
  end
end
```

```bash
1) User can see specific article Article displays title
     Failure/Error: click_on 'A breaking news item'
     
     ActionController::MissingExactTemplate:
       ArticlesController#show is missing a template for request formats: text/html
```

create show.html.erb

```html
<p>A breaking news item</p>
<p>Some breaking action</p>
```

hard coded blablabla

```rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end
end
```

```html
<p><%= @article.title %></p>
<p><%= @article.content %></p>
```

DONE!!!
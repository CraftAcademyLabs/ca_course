---
title: "Rails 5 - Active Storage"
subtitle: "Attaching Files To Active Model"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Elasticsearch, Rails"
keywords: [Elasticsearch, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

### Active Storage

Active Storage is a new addition to Rails \(introduced in Rails 5.2\) that allows for attaching files to Active Record objects. Attachements can be stored locally or uploaded to a cloud storage service like Amazon S3, Google Cloud Storage, or Microsoft Azure Storage.



Working on a separate branch \(not `master`\)

Active Storage comes with some custom migrations \(investigate why\). Here is what they do, according to framework’s README:

_“Active Storage uses polymorphic associations via the Attachment join model, which then connects to the actual Blob. Blob models store attachment metadata \(filename, content-type, etc.\), and their identifier key in the storage service."_

Copy the migrations to your `db/migrations` folder:

```bash
$ rails active_storage:install:migrations
```

### Create the `Article` model

```bash
$ rails g model article title:string body:text

      invoke  active_record
      create    db/migrate/20180308211352_create_articles.rb
      create    app/models/article.rb
      invoke    rspec
      create      spec/models/article_spec.rb
      invoke      factory_bot
      create        spec/factories/articles.rb
```

These basic specs can tell us if the Article model is doing what we want it to do:

```ruby
RSpec.describe Article, type: :model do
  describe 'Factory' do
    it 'is valid' do
      expect(create(:article)).to be_valid
    end
  end

  describe 'DB Table' do
    it {is_expected.to have_db_column(:title).of_type(:string)}
    it {is_expected.to have_db_column(:body).of_type(:text)}
  end

  describe 'Attachment' do
    it 'is valid  ' do
      subject.image.attach(io: File.open(fixture_path + '/dummy_image.jpg'), filename: 'attachment.jpg', content_type: 'image/jpg')
      expect(subject.image).to be_attached
    end
  end
end
```

In order to have these specs go green, let's start with adding an image to the Article model

```ruby
class Article < ApplicationRecord
  has_one_attached :image
end
```

We don't have to create an `Image`  model. Active Storage uses the Blob and Attachment under the hood to give us `article.image`.

### ArticlesController

```bash
$  rails g controller Articles new show
create  app/controllers/articles_controller.rb
       route  get 'articles/new'
              get 'articles/show'
      invoke  haml
      create    app/views/articles
      create    app/views/articles/new.html.haml
      create    app/views/articles/show.html.haml
      invoke  rspec
```

I always modify my routes to use the `resources` keyword:

```ruby
Rails.application.routes.draw do
  resources :articles, only: [:new, :create, :show]
end
```

### Routes

With this setup, our routes table looks something like this:

```bash
$ rails routes
                   Prefix Verb URI Pattern                                                                       Controller#Action
                 articles POST /articles(.:format)                                                               articles#create
              new_article GET  /articles/new(.:format)                                                           articles#new
                  article GET  /articles/:id(.:format)                                                           articles#show
       rails_service_blob GET  /rails/active_storage/blobs/:signed_id/*filename(.:format)                        active_storage/blobs#show
     rails_blob_variation GET  /rails/active_storage/variants/:signed_blob_id/:variation_key/*filename(.:format) active_storage/variants#show
       rails_blob_preview GET  /rails/active_storage/previews/:signed_blob_id/:variation_key/*filename(.:format) active_storage/previews#show
       rails_disk_service GET  /rails/active_storage/disk/:encoded_key/*filename(.:format)                       active_storage/disk#show
update_rails_disk_service PUT  /rails/active_storage/disk/:encoded_token(.:format)                               active_storage/disk#update
     rails_direct_uploads POST /rails/active_storage/direct_uploads(.:format)                                    active_storage/direct_uploads#create
```

Firing up the server \(`rails s`\) and going through the process of creating an article worked, so I set off to write an acceptance test. I ended up with this simple scenario:

```gherkin
Feature: User can create article with image attachment
  As an Author
  In order be able to add content to the news service
  I would like to be able to publish articles with an image


  Scenario: Auth creates an article
    Given I am on the create article page
    And I fill in "Title" with "Awesome news"
    And I fill in "Content" with "Lorem ipsum"
    And I attach a file
    And I click "Create Article"
    Then I should be on the article page for "Awesome news"
```

And my step definitions:

```ruby
Given("I am on the create article page") do
  visit new_article_path
end

And("I fill in {string} with {string}") do |field, value|
  fill_in field, with: value
end

And("I attach a file") do
  attach_file('article_image', "#{::Rails.root}/spec/fixtures/dummy_image.jpg")
end

And("I click {string}") do |value|
  click_on value
end

Then("I should be on the article page for {string}") do |article_title|
  article = Article.find_by(title: article_title)
  expect(current_path).to eq article_path(article)
end
```

Our `new.html.haml` looks like this:

```haml
= form_with model: @article, local: true do  |form|
  = form.label :title
  = form.text_field :title
  = form.file_field :image
  = form.label :body, 'Content'
  = form.text_area :body
  = form.s
```

And the `show` page, that the create action will redirect to, looks like this:

```haml
%h1= @article.title
%p= @article.body
= image_tag @article.image
```

So, time to look at the controller action. Pretty straight forward and simple:

```ruby
class ArticlesController < ApplicationController
  def new
    @article = Article.new
  end

  def show
    @article = Article.find(params[:id])
  end

  def create
    article = Article.create(article_params)
    redirect_to article
  end

  private

  def article_params
    # note that we add ':image' to permitted params
    # that will add the image to the instance of Article
    # we are working on 
    params.require(:article).permit(:title, :body, :image)
  end
end
```

**And now, it all works. Gotta love Rails!**

### Final words
Active Storage brings a lot of powerful geatures to the table. However, there is no build in validations (of i.e. the format of the attachment) which means you, the developer, need to handle validations on your own. 

In the example above, we added configured the Article model to accept ONE attachment. You can, of course, add MANY attachments to a model using the `has_many_attached` association. You can read about that, and many other topics in consequent guides or check out the [official API docs](https://edgeguides.rubyonrails.org/active_storage_overview.html).


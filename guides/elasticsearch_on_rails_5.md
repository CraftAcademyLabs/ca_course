---
title: "Seek, And Ye Shall Find..."
subtitle: "Rails 5 - Elasticsearch"
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


# Search functionality in Rails using Elasticsearch and Chewy

Elasticsearch is a powerful and flexible, open source, RESTful HTTP, distributed interface for data indexing and real-time search.

## The basic guide to Elasticsearch
[Elasticsearch - https://www.elastic.co/](https://www.elastic.co/) has several document-related concepts. The first is that of an `index`, which consists of a set of `documents`, which can be of several `types`. Every document has a set of `fields`. Each `field` is analyzed by Elasticsearch independently and its analysis options are stored in the `mapping` for its type.  

## Chewy - Elasticsearch for Ruby 
[Chewy (https://github.com/toptal/chewy)](https://github.com/toptal/chewy) is an alternative to the `elasticsearch-model` gem and provides a wrapper for the Elasticsearch-Ruby client, making it more powerful and giving you tighter integration with Rails. Chewy utilizes the bulk Elasticsearch API for full reindexing and index updates. It also utilizes the concept of atomic updates, collecting changed objects within an atomic block and updating them all at once. 

## Getting started...

For training purposes, we will set up Elasticsearch and Chewy and do some manual testing in our console. 

Once we are a bit familiar with the basic setup of the search engine, we will go back to our "regular" BDd approach, and start telling the story - writing the acceptace test for our search functionality. 

As we run the cucumber scenario, we'll work our way through the stack and implement a solution and bring the power of Elasticsearch to our Rails 5.x application. 

## ...with Elasticsearch

Install Elasticsearch on your computer. 

```bash
$ brew install elasticsearch
```

Start an instance in a separate terminal tab (you can also run it as a background service).

```bash
$ elasticsearch

// or 

$ brew services start elasticsearch
```

Visit `http://localhost:920` in your browser. You should get a json response with the current version of Elasticsearch running on your computer. 

```json
{
name: "UUaBwml",
cluster_name: "elasticsearch_thomas",
cluster_uuid: "dT6LcenqQbKFF1ngmj9FuQ",
- version: {
    number: "6.2.4",
    build_hash: "ccec39f",
    build_date: "2018-04-12T20:37:28.497551Z",
    build_snapshot: false,
    lucene_version: "7.2.1",
    minimum_wire_compatibility_version: "5.6.0",
    minimum_index_compatibility_version: "5.0.0"
},
tagline: "You Know, for Search"
}
```

## ...with Chewy

Add Chewy to your `Gemfile` and run `bundle`.

```ruby
gem 'chewy'
```

Use the renerator provided by the gem to create the configuration files. 

```bash
$ rails g chewy:install
```

That will create a settings file in your applications configuration folder (`config/chewy.yml`).

```yaml
# separate environment configs
test:
  host: 'localhost:9250'
  prefix: 'test'
development:
  host: 'localhost:9200'
```

We also want to set create an initializer for Chewy and set the `root_strategy` for indexing.

```bash 
$ touch config/initializers/chewy.rb
```

```ruby
Chewy.root_strategy = :atomic
```

You can read more about index updating strategies in the [Chewy documentation (https://github.com/toptal/chewy#index-update-strategies)](https://github.com/toptal/chewy#index-update-strategies).

In our demo application, we have a `Recipe` model, with two attributes that we'll use for search. 

```ruby
class Recipe < ApplicationRecord
  validates :title, presence: true, length: {minimum: 3}
  validates :description, presence: true
end
```

We will create a new folder and file in the `app` folder, that will store our index setting for Recipes. 

```bash 
$ mkdir config/indexes
$ touch config/indexes/recipe_index.rb
```

We will create a `RecipeIndex` that will inherit from `Chewy::Index` and hold the rules on how we want Elsaticsearch to search the instances of the `Recipe` model. 

```ruby
class RecipeIndex < Chewy::Index
    define_type Recipe.all do 
        field :title
        field :description
    end
end
```

With this vary basic setup, we can move on and try out the search functionality in our rails console. 

## Performing a search

Make sure that your database is seeded with some data and open the console.

```bash
$ rails c
```

```ruby
[1] pry(main)> RecipesIndex.create!
=> {"acknowledged"=>true, "shards_acknowledged"=>true, "index"=>"recipes"}


[2] pry(main)> RecipesIndex.import
   (0.2ms)  SELECT  DISTINCT "recipes"."id" FROM "recipes" ORDER BY "recipes"."id" ASC LIMIT $1  [["LIMIT", 1000]]
  Recipe Load (0.5ms)  SELECT "recipes".* FROM "recipes" WHERE "recipes"."id" IN (1, 2, 3, 4, 5)
  RecipesIndex::Recipe Import (54.2ms) {:index=>5}
=> true


[3] pry(main)> search = RecipesIndex.query(match: { description: 'turkey ketchup'} )
=> <RecipesIndex::Query {:index=>["recipes"], :type=>["recipe"], :body=>{:query=>{:match=>{:description=>"turkey ketchup"}}}}>

```

For better results, we probably want to search both the `:title` and `:description` fields. For that we can use a `multi_match` query (Note that we changed the search query here)

```ruby
[4] pry(main)> multiple_field_search = RecipesIndex.query(multi_match: { query: 'turkey Meat', fields: [:title, :description], type: :cross_fields} )
=> <RecipesIndex::Query {:index=>["recipes"], :type=>["recipe"], :body=>{:query=>{:multi_match=>{:query=>"turkey Meat", :fields=>[:title, :description], :type=>:cross_fields}}}}>

[5] pry(main)> multiple_field_search.objects.count
  RecipesIndex::Recipe Search (8.4ms) {:index=>["recipes"], :type=>["recipe"], :body=>{:query=>{:multi_match=>{:query=>"turkey Meat", :fields=>[:title, :description], :type=>:cross_fields}}}}
  Recipe Load (0.6ms)  SELECT "recipes".* FROM "recipes" WHERE "recipes"."id" IN (2, 4)
=> 2

```

## What's next?

There is a massive amount of possibilities with Elasticsearch and Chew and we only scratched the surface with the two examples above. 

**This ends the first part of the Elasticsearch Guide** In the next part, we'll start integrating the search functionality into our Rails application BDD style. 

## Extra

### Trubleshooting

#### Running Elasticsearch

If you have issues with starting Elasticsearch on your `localhost`, you might already have an instance up and running as a background service. 

Stop the service and start it in a separate terminal tab:
```bash
$ brew stop elasticsearch

Stopping `elasticsearch`... (might take a while)
==> Successfully stopped `elasticsearch` (label: homebrew.mxcl.elasticsearch)

$ elasticsearch

Java HotSpot(TM) 64-Bit Server VM warning: Cannot open file logs/gc.log due to No such file or directory

...
```

#### Installing

If you run into issues with installing Elasticsearch, you might need to clear a version installed and need to remove it from your system together with some configuration settings.

```bash
$ brew remove elasticsearch
$ rm -rf /usr/local/var/elasticsearch
$ rm -rf /usr/local/etc/elasticsearchch
```
and start again:

```bash
$ brew install elasticsearch
```

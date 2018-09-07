---
title: "Middleman"
subtitle: "Accessing data"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Middleman, HAML"
keywords: [Middleman, HAML]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...


## Accessing data

Middleman allows you to create `.yml`, `.yaml` or `.json` files in a folder called `data` and makes this information available in your templates. The `data` folder should be placed in the root of your project i.e. in the same folder as your project's `source` folder.

Let's create a simple test for listing some project information on your `index` page. 

Run these in the terminal:

`mkdir spec/features`

`touch spec/features/index_spec.rb`

Add this code to the feature file:



```ruby
# spec/features/index_spec.rb

describe 'Index Page', type: :feature do
  it 'displays project list' do
    expect(page).to have_css '.projects'
    within '.projects' do
      expect(page).to have_content 'My First Website'
      expect(page).to have_content 'FizzBuzz'
    end
  end
end
```

In order to make this test go green, we need to got through some steps:
1. Create the `data` folder
2. Add a `projects.yml` in the `data` folder
3. Add some project information in the `YML` format to the `projects.yml` file
4. Display the project information on our `index.hml.haml` template


```yml
# data/projects.yml

- name: My First Website
  description: Simple HTML5 site I've build during the PrepCourse

- name: FizzBuzz
  description: My first Test driven ruby project
```


```haml
# source/index.html.haml

.projects
  - data.projects.each do |project|
    %h4= project[:name]
    %p= project[:description]
```

Note that Middleman knows how to access the data folder and the `projects.yml` file as long as you use the right file name in your code.

**Knowing this, you can extract information about your projects from the template and store it in a YAML file and display it on any page you want.**

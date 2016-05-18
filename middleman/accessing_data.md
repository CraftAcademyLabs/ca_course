## Accessing data

Middleman allows you to create `.yml`, `.yaml` or `.json` files in a folder called `data` and makes this information available in your templates. The `data` folder should be placed in the root of your project i.e. in the same folder as your project's `source` folder.

Let's add a simple test for listing some project information on your `index` page. 

!FILENAME spec/features/index_spec.rb
```ruby 
  it 'displays project list' do
    expect(page).to have_css '.projects'
    within '.projects' do
      expect(page).to have_content 'My First Website'
      expect(page).to have_content 'FizzBuzz'
    end
  end
```

In order to make this test go green, we need to got through some steps:
1. create the `data` folder
2. add a `projects.yml` in the `data` folder
3. add some project information in the `YML` format to the `projects.yml` file
4. display the project information on our `index.hml.haml`template

!FILENAME data/projects.yml
```yml
- name: My First Website
  description: Simple HTML5 site I've build during the PrepCourse

- name: FizzBuzz
  description: My first Test driven ruby project
```

!FILENAME source/index.html.haml
```haml
.projects
  - data.projects.each do |project|
    %h4= project[:name]
    %p= project[:description]
```

**Knowing this, you can extract information about your projects from the template and store it in a YAML file and display it on any page you want.** 

It's sometimes necessary to create non-RESTful controller action and routes in Rails.
What is the difference between collection routes and member routes in Rails?

For example,

```ruby
resources :photos do
  member do
    get :preview
  end
end
``` 
versus

```ruby
resources :photos do
  collection do
    post :search
  end
end
```

A member route will require an ID because it acts on a member. A collection route doesn't because it acts on a collection of objects. `/preview`  is an example of a member route because it acts on (and displays) a single resource. Search is an example of a collection route, because it acts on (and displays) a collection of resources.

The path helpers created above will result in:

```
preview_photo_path(resource)
search_photos_path
```



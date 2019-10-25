# Routes

## Quick Cheatsheet

*Mostly taken from [devhints](https://devhints.io/rails-routes).*

### Resources

```ruby
resources :books

# BooksController:
# index  =>    GET /books
# new    =>    GET /books/new
# create =>   POST /books/new
# show   =>    GET /books/:id
# edit   =>    GET /books/:id/edit
# update =>    PUT /books/:id
# delete => DELETE /books/:id
#
# Helpers:
# new_book_path
# book_path(id)
# edit_book_path(id)
```

### Member and Collection

`collection` is for routes on the collection.

`member` is for routes on a specific member.

```ruby
Rails.applications.routes.draw do
  resources :events do
    collection do
    	post :validate # localhost:3000/events/validate
    end
    
    member do
      post :publish # localhost:3000/events/1/publish
    end
end
```

### Options

```ruby
resources :photos,
  path_names: { new: 'brand_new' }    # /photos/1/brand_new
  path: 'postings'                    # /postings
  only: :index
  only: [:index, :show]
  except: :show
  except: [:index, :show]

  shallow: true                       # also generate shallow routes
  shalow_path: 'secret'
  shallow_prefix: 'secret'
```

### Single Resource

```ruby
resource :coder

# CodersController:
# new    =>    GET /coder/new
# create =>   POST /coder/new
# show   =>    GET /coder
# edit   =>    GET /coder/edit
# update =>    PUT /coder
# delete => DELETE /coder
```

### Matching

```ruby
match 'photo/:id' => 'photos#show'  # /photo/what-is-it
match 'photo/:id', id: /[0-9]+/     # /photo/0192
match 'photo/:id' => 'photos#show', constraints: { id: /[0-9]+/ }
match 'photo/:id', via: :get
match 'photo/:id', via: [:get, :post]

match 'photo/*path' => 'photos#unknown'    # /photo/what/ever

# params[:format] == 'jpg'
match 'photos/:id' => 'photos#show', :defaults => { :format => 'jpg' }
```

### Redirect

```ruby
match '/stories' => redirect('/posts')
match '/stories/:name' => redirect('/posts/%{name}')
```

### Named Routes

```ruby
# logout_path
match 'exit' => 'sessions#destroy', as: :logout
```

### Scopes

```ruby
scope 'admin', constraints: { subdomain: 'admin' } do
  resources ...
end
```



## Nested Resources (routes)

Assuming an event has many registrations and we want registration routes to be nested under an event, e.g. `localhost:3000/events/1/registrations`, we can do:

```ruby
Rails.applications.routes.draw do
  resources :events do
    resources :registrations
  end
end
```



## Splitting Up Big Routes Files

*(Mostly taken from [Matt Boldt's blog post](https://mattboldt.com/separate-rails-route-files/))*

[GitLab's route files](https://gitlab.com/gitlab-org/gitlab/tree/master/config/routes) are also a great example.

**First you have to make a new `draw` method into Rails's routing mapper via an initializer**

```ruby
# config/initializers/routing_draw.rb

# Adds draw method into Rails routing
# It allows us to keep routing splitted into files
class ActionDispatch::Routing::Mapper
  def draw(routes_name)
    instance_eval(File.read(Rails.root.join("config/routes/#{routes_name}.rb")))
  end
end
```

**Update your `config/routes.rb` with the names of files in `config/routes/*.rb`**

```ruby
# config/routes.rb
MyApp::Application.routes.draw do
  draw :api_v1
  draw :api_v2
  draw :admin
end
```

**New route files**

```ruby
# config/routes/api_v1.rb
namespace :api_v1 do
  # lots of routes
end

# config/routes/api_v2.rb
namespace :api_v2 do
  # lots of routes
end

# config/routes/admin.rb
namespace :admin do
  # lots of routes
end
```


# Generating Your First Model Controller And View (OUT OF DATE)

Now that you understand the RESTful relationship between databases, models, controllers, and views you are ready to begin adding to your application. Rails has several scripts that are designed to help you create RESTful applications, one of which is the generator. Using it we are going to generate a RESTful controller and model with views. The first thing to do is create the model.

```
user$ rails generate model post title:string content:text
      invoke  active_record
      create    db/migrate/20120417214153_create_posts.rb
      create    app/models/post.rb
      invoke    test_unit
      create      test/unit/post_test.rb
      create      test/fixtures/posts.yml
```

This created the migration and model, as well as test units (for advanced users). Next we have to create the database and migrate it to the current version.

```
user$ rake db:create
user$ rake db:migrate
==  CreatePosts: migrating ====================================================
-- create_table(:posts)
   -> 0.0023s
==  CreatePosts: migrated (0.0024s) ===========================================
```

As you can see, the database was created and a table was created called posts. The next step is to create a controller with the 7 RESTful functions.

```
user$ rails generate controller posts index show new create edit update destroy
      create  app/controllers/post_controller.rb
       route  get "posts/destroy"
       route  get "posts/update"
       route  get "posts/edit"
       route  get "posts/create"
       route  get "posts/new"
       route  get "posts/show"
       route  get "posts/index"
      invoke  erb
      create    app/views/posts
      create    app/views/posts/index.html.erb
      create    app/views/posts/show.html.erb
      create    app/views/posts/new.html.erb
      create    app/views/posts/create.html.erb
      create    app/views/posts/edit.html.erb
      create    app/views/posts/update.html.erb
      create    app/views/posts/destroy.html.erb
      invoke  test_unit
      create    test/functional/posts_controller_test.rb
      invoke  helper
      create    app/helpers/posts_helper.rb
      invoke    test_unit
      create      test/unit/helpers/posts_helper_test.rb
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/posts.js.coffee
      invoke    scss
      create      app/assets/stylesheets/posts.css.scss
```

This command has done a lot so I’ll walk you through it.

The first line is the controller that was actually created.

The lines that say route are modifications made to the config/routes.rb file. We actually don’t want the routes the generator created since we can consolidate them into a single route.

In the file config/routes.rb the 7 created routes should be deleted and can be replaced by:

```
resources :posts
```

The next lines created the views. Because of how the generator works, much like the routes, it did not create the views quite correctly. We only need index, show, new, and edit so the other ones can be deleted.

```bash
user$ rm app/views/posts/create.html.erb
user$ rm app/views/posts/update.html.erb
user$ rm app/views/posts/destroy.html.erb
```

Then there is the test unit, helper, and assets, which we won’t cover right now.

We’re going to begin with the index view. We will create it using a simple table that displays all posts and a link to our new post view.

```html+erb
<h1>Listing posts</h1>

<table>
  <tr>
    <th>Title</th>
    <th>Content</th>
    <th></th>
    <th></th>
    <th></th>
  </tr>

<% @posts.each do |post| %>
  <tr>
    <td><%= post.title %></td>
    <td><%= post.content %></td>
    <td><%= link_to 'Show', post %></td>
    <td><%= link_to 'Edit', edit_post_path(post) %></td>
    <td><%= link_to 'Destroy', post, :confirm => 'Are you sure?', :method => :delete %></td>
  </tr>
<% end %>
</table>
```

it is important to point out that all rails code must be inside `<% %>` tags or `<%= %>` tags. The difference is that with the `=` sign, the output of the rails code will be put into the html. Without it, the output is not shown.

Now that we have our view, we need to create the controller function for index that will populate the variables. The only variables that cross between controllers and views are the variables preceded by an `@` symbol. This means we need our `@posts` variable to be a hash of all posts.

In our app/controllers/posts_controller.rb file we need the index action to populate the variable.

```ruby
def index
  @posts = Post.all
end
```

And that’s all we need for our application to run. It still won’t do very much right now though since there are no posts in the database, and no way to create new ones. That will be discussed in the next post.

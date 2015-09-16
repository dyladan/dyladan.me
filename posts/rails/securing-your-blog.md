# Securing Your Blog (OUT OF DATE)

Now that you can create, edit, and delete posts, it is important that you make it so that you are the only person who can do so. You wouldn’t want random people deleting your posts now would you? In order to do this we are going to use a gem called devise.

```
user$ gem install devise
Successfully installed devise-x.x.x
1 gem installed
```

Add the following line to your Gemfile

```ruby
gem 'devise'
```

And run the `bundle` command

Now we need to run the devise install generator

```
user$ rails g devise:install
```

Make sure you follow all of the instructions it gives you. After that we need to create a user model using devise.

```
user$ rails g devise user
      invoke  active_record
      create    db/migrate/20120419192356_devise_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/unit/user_test.rb
      create      test/fixtures/users.yml
      insert    app/models/user.rb
       route  devise_for :users
```

As you can see, it created a model and a migration. Run the migration

```
user$ rake db:migrate
```

Devise expects you to have a `root :to` route, so I am going to route it to my posts index. To do this simply add this line to `config/routes.rb`:

```ruby
root :to => 'posts#index'
```

Now we need to configure the options we want for our user. Edit the newly created user model. I am going to go with a fairly minimal user model since I don’t want to deal with confirmation emails or other such nonsense. We will have to set it up as registerable now so I can create a user for myself but I am going to turn this off later.

```ruby
class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable, :rememberable, :trackable, :validatable
  attr_accessible :email, :password, :password_confirmation, :remember_me
end
```

The next thing to do is add links to log in and register buttons, as well as views for related forms. This can all be generated with the single command:

```
user$ rails g devise:views
```

Now we need to link to the newly created views. In an appropriate place in your layout, add the following to create links to login/register/logout/edit:

```html+erb
<% if user_signed_in? %>
  <%= link_to('Logout', destroy_user_session_path, :method => :delete) %>
<% else %>
  <%= link_to('Login', new_user_session_path)  %>
<% end %>
<% if user_signed_in? %>
  <%= link_to('Edit registration', edit_user_registration_path) %>
<% else %>
  <%= link_to('Register', new_user_registration_path)  %>
<% end %>
```

Now we can start up the server with

```
user$ rails s
```

and browse to `localhost:3000/posts` to see your blog. You should now be able to register a user. After you create your user, you may want to remove `:registerable` from your users model, and the links to registration in your layout.

Now that we have a user, we can start controlling what people can and cannot do when they are not logged in. In order to do this we should begin by changing what users see when they are not logged in. The links we created to create, edit, and delete posts need to be modified using if statements like so:

```html+erb
<h1>Listing posts</h1>

<table>
  <tr>
    <th>Name</th>
    <th>Title</th>
    <th>Content</th>
    <th></th>
    <th></th>
    <th></th>
  </tr>

<% @posts.each do |post| %>
  <tr>
    <td><%= post.name %></td>
    <td><%= post.title %></td>
    <td><%= post.content %></td>
    <td>
      <%= link_to 'Show', post %>
    </td>
    <td>
      <% if user_logged_in? -%>
        <%= link_to 'Edit', edit_post_path(post) %>
      <% end -%>
    </td>
    <td>
      <% if user_logged_in? -%>
        <%= link_to 'Destroy', post, :confirm => 'Are you sure?', :method => :delete %>
      <% end -%>
    </td>
  </tr>
<% end %>
<br />
<%= link_to 'New Post', new_post_path %>
</table>
```

It is also important to note however that this just hides the link, and if an unauthorized user were to browse to the correct url, they would be able to do all of the tasks they are not allowed to do. We fix this in the posts controller. Add this line to the posts controller:

```ruby
before_filter :authenticate_user!, :except => [:index, :show]
```

You do have to make sure that in any view you make, you have an if statement that checks to make sure the user is logged in before displaying any sensitive content or links. Also, making your views check the user is not good enough, you should ALWAYS lock down your controllers also to avoid users guessing at URLs and finding sensitive content.

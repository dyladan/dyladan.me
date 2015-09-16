# Making Your Blog Useable (OUT OF DATE)

Now it is time to make it so you can create and edit blog posts. In order to do this we will need to edit the controller and view.

Controller
---------

First we need to populate the functions needed to create and edit posts. These are the new, create, edit, and update functions. You should edit them to reflect this:

```ruby
def new
  @post = Post.new
end
def create
  @post = Post.new(params[:post])
  @post.save
  redirect_to posts_path(@post)
end
def edit
  @post = Post.find(params[:id])
end
def update
  @post = Post.find(params[:id])
  if @post.update_attributes(params[:post])
    format.html { redirect_to @post, :notice => 'Post was successfully updated.' }
  else
    format.html { render :action => "edit" }
  end
end
def destroy
  @post = Post.find(params[:id])
  @post.destroy

  redirect_to posts_url
  return
end
```

Everything here is pretty self explanatory. I’ve already explained the difference between new and create as well as edit and update here.

The :notice => … will become important to display error/success messages/flashes

View
----

in your index view you have to make a link to create posts

Add this to the end of your view

```html+erb
<br />

<%= link_to 'New Post', new_post_path %>
```

Now we just need edit and new forms. This is a part of the real beauty of rails. We can write a single form that will not only edit AND create posts, but will dynamically change itself based on what you are trying to do using the magic of partials. A partial is in the same folder as the view and is named with a preceding underscore. It is called with the render directive. Create a file in `views/posts` called `_form.html.erb` This is mine. It also makes use of the messages we made earlier for editing and creating posts in the controller.

```html+erb
<%= form_for(@post) do |post_form| %>
  <% if @post.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h2>

      <ul>
      <% @post.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= post_form.label :title %><br />
    <%= post_form.text_field :title %>
  </div>
  <div class="field">
    <%= post_form.label :content %><br />
    <%= post_form.text_area :content %>
  </div>
  <div class="actions">
    <%= post_form.submit %>
  </div>
<% end %>
We also need a new.html.erb

<h1>New post</h1>

<%= render 'form' %>

<%= link_to 'Back', posts_path %>
and an edit.html.erb

<h1>Editing post</h1>

<%= render 'form' %>

<%= link_to 'Show', @post %> |
<%= link_to 'Back', posts_path %>
Now that we have created a link to the show method, we should create that too. show.html.erb

<p id="notice"><%= notice %></p>

<p>
  <b>Title:</b>
  <%= @post.title %>
</p>

<p>
  <b>Content:</b>
  <%= @post.content %>
</p>

<br />

<%= link_to 'Edit', edit_post_path(@post) %> |
<%= link_to 'Back', posts_path %>
```

Now you have a fully functioning application. To view it simply type

```
user$ rails s
```

And browse to localhost:3000 in your web browser.

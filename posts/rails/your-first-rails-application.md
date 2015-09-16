# Your First Rails Application (OUT OF DATE)

So you’ve installed RVM, installed Ruby, and installed rails. Now what?
I’m going to take you through the first steps to develop your first rails application.
It is going to be a simple blog with a static home page. In future episodes,
we will expand on this blogging application, so don’t delete it.

Creating the application
------------------------

The first step is to create an empty rails application. We can do this with
the rails new command. The command is in the form: `rails new APP_NAME`.
I am going to use the name rails_blog for mine

    user$ rails new rails_blog

next cd into the directory

    user$ cd rails_blog

You may notice that this command has created many files. Here is the directory tree with descriptions for folders and files.

```
stuff
```

Now that we have our application set up we need it to do something. In order for this to do anything we need three things: model, controller, and view.

Models
------

Models are the building block of rails. They determine the relationship between the controller and the database. A model for something like a blog might be called post. This would mean the database that it connects to would be called posts and the controller would be called the posts_controller or the post_controller (depending on how you have your routes set up)

Controllers
-----------

Controllers do all of the back-end computation and logic. They are responsible for formulating database queries and sending them to the model, which interfaces with the actual database. The standard rails application with RESTful routing will have controllers that have the following functions

- new
  - calls the new.html.erb view
  - will create a new record but will not save it
  - typically posts results to create function
- create
  - does not call a view
  - will create and save a new record based on posted parameters from new
- index
  - calls index.html.erb view
  - typically displays a list or page that shows all instances of a model
- show
  - calls show.html.erb view
  - typically displays a single instance of a model
- edit
  - calls edit.html.erb view
  - typically shows a form to edit an instance of a model
  - typically posts results to update function
- update
  - does not call a view
  - updates an instance of a model based on posted parameters from edit
- destroy
  - deletes an instance of a model

Views
-----

Views are in charge of what displays to the user. They typically have the extension .html.erb The reason they have multiple extensions is that rails can process files more than one time. The more extensions you add on, the more preprocessors the file will be run through. This means that by adding .erb to any file, you can now put inline ruby script in the file and it will be processed. This is important in views because we want to display dynamic content and the view has to be able to get output from the controller (which, in turn has made the queries to the database).

Typically (as explained in the controllers) the standard views of a RESTful controller are

- index.html.erb
- show.html.erb
- new.html.erb
- edit.html.erb

Their names are pretty self explanatory

Migrations
----------

Migrations are files that tell the rake script what changes to make to the database such as adding and dropping tables and columns.

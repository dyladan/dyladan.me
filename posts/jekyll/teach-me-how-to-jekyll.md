# Teach Me How To Jekyll

## The basics of Jekyll - First site, Posting, and YAML front-matter

This is going to be the first in a series of posts about Jekyll over the course of the next.... whenever.
This is going to be an instructional series, but not in the traditional sense. You see, I know little to
nothing about Jekyll. I'm going to be teaching myself Jekyll over the course of the next \[see above timeframe\]
and in order to solidify my understanding I'm going to be blogging my progress on a Jekyll site. When I'm done
I hope this will be an educational resource particularly suited to beginners because it will be written from
a beginner's point of view.

I'll begin by listing things I already know that I believe will be helpful based on what I know about Jekyll.
This will become, if not a list of prerequisites, then at least a list of things I will not be teaching you.
I may edit it later, but for now here it is.

- markdown
- ruby
- rubygems
- HTML
- CSS
- english

side note: If I make any edits to previous posts I will be sure to include a list of edits on the footer of the
post being edited, as well as a comment on the most recent post so it will be easy to identify changed parts.

And so we begin. The first thing to do is to install Jekyll and create a basic site. In this section
substitute `mysite` for a site name of your choosing.

```bash
gem install jekyll #install the jekyll gem
jekyll new mysite  #create a new site called mysite
cd mysite          #enter site directory
```

Now you should have a directory structure like this:

```
.
├── _config.yml
├── css
│   ├── main.css
│   └── syntax.css
├── index.html
├── _layouts
│   ├── default.html
│   └── post.html
└── _posts
    └── 2014-01-01-welcome-to-jekyll.markdown

3 directories, 7 files
```

Believe it or not, this is actually a fully functioning jekyll site. You can run it with the command `jekyll serve`.

Note: There used to be a picture here on the old site.

At this point, you may be saying "HEY! How did you put that picture in there?" It's really simpler than you may think.
Jekyll will copy any file or folder not beginning with an underscore into your site directory. All I did was create
the folder `./images` and put my image in there. After that I used the `![alt text](/path/to/image)` markdown syntax.

This brings me to my next point, organization. Some people like to have everything accessible from their base folder
but I don't roll that way. I like deep directory structures that read like books. That way, there is never any
ambiguity about what a file is or where it is supposed to go. If there isn't a folder that fits a situation, make one.
This also has the added benefit of keeping a clean home directory. With this in mind, we're going to do a little
reorganization. This is entirely preference, but here is the folder structure I use.

```
.
├── assets
│   ├── css
│   │   ├── main.css
│   │   └── syntax.css
│   └── images
│       └── jekyll-default-index.png
├── _config.yml
├── index.html
├── _layouts
│   ├── default.html
│   └── post.html
└── _posts
    ├── 2014-01-01-teach-me-how-to-jekyll.markdown
    └── _drafts
        └── 2014-01-01-welcome-to-jekyll.markdown

6 directories, 9 files
```

Don't forget to change all references to files you may have moved. The css files I moved are referenced in
`./_layouts/default.html`.

The attentive may have noticed the `_drafts` folder I put in the `_posts` folder. Remember what I said earlier about
Jekyll copying any file that doesn't start with an underscore? If the file *does* begin with an underscore,
it is completely ignored (unless it is one of the special files/folders used by Jekyll). This means we can keep
a folder named `_drafts` in our `_posts` folder and it will be completely ignored. I've used it to store the default
first post so I can refer to it for syntax.

You also see this post in the source tree. In order to create a post, you create a file in the `_posts` directory
with the format `yyyy-mm-dd-slug-title.extension`.  I used the markdown extension because I like markdown and it was
the Jekyll default. Jekyll parses markdown using Redcarpet, which was created and is used and maintained by GitHub.
In order to be read properly by Jekyll, your post needs what is called YAML front-matter. This is the front-matter
used for this post.

```yaml
---
layout: post
title: Teach Me How To Jekyll
---
```

As you can see, it is pretty basic. The `layout` tells Jekyll to parse this content and insert it into the
`post` layout at `./_layouts/post.html`. The `post` layout in turn uses the `default` layout. Layouts can be
named anything and are always put into the `./_layouts` folder. You can add any additional metadata in the form
of YAML and it will be processed and available for use in layouts. The layout that is automatically generated
by Jekyll only uses `title` and `date` (`date` is generated from the file name but you can override it in the
front-matter).

Lets add a tag line to our posts so we can add a quick description of what our posts are and excercise our knowledge
of Jekyll along the way. First, add the `description` key to our YAML front-matter and give it a value.

```yaml
---
layout: post
title: Teach Me How To Jekyll
description: The basics of Jekyll - First site, Posting, and YAML front-matter
---
```

Next, we need to use this key in our post template. Jekyll uses the liquid templating engine, which means we
can quickly and easily add this key. My `./_layouts/post.html` now looks like this (note the use of the `if`
statement so we only display the `p` tag when the content actually exists):

```html
---
layout: default
---
<h2>{{ page.title }}</h2>
{% if page.description %}
<p class="description">{{ page.description }}</p>
{% endif %}
<p class="meta">{{ page.date | date_to_string }} - {{ page.content | number_of_words }} words</p>

<div class="post">
{{ content }}
</div>
```

I also added in a word count using a liquid filter which I'll get into more later. So now that we have post specific
variables, what about site wide variables? Any variables set in the `./_config.yml` file will be available to liquid
using `site.[variable]`. We can use this to our advantage for certain things that might change later, especially if
they show up in multiple places. Add the following to the end of your `./_config.yml` substituting my information for yours.

```yaml
admin:
  name: Daniel Dyla
  occupation: Programmer
  github: dyladan
  twitter: dyladan
  email: dyladan@gmail.com
```

Next, we have to add our variables into the template. This time we are going to be changing the `default` layout. Locate
and edit the `./_layouts/default.html` file. We need to change any hardcoded references to any of the variables we just
made to the liquid variable equivalents. Here is what mine looks like but feel free to add any custom variables you want
and play around with different ways to use them.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
        <title>{{ page.title }}</title>
        <meta name="viewport" content="width=device-width">

        <!-- syntax highlighting CSS -->
        <link rel="stylesheet" href="/assets/css/syntax.css">

        <!-- Custom CSS -->
        <link rel="stylesheet" href="/assets/css/main.css">

    </head>
    <body>

        <div class="site">
          <div class="header">
            <h1 class="title"><a href="/">{{ site.name }}</a></h1>
            <a class="extra" href="/">home</a>
          </div>

          {{ content }}

          <div class="footer">
            <div class="contact">
              <p>
                {{ site.admin.name }}<br />
                {{ site.admin.occupation }}<br />
                {{ site.admin.email }}
              </p>
            </div>
            <div class="contact">
              <p>
                <a href="https://github.com/{{ site.admin.github }}">github.com/{{ site.admin.github }}</a><br />
                <a href="https://twitter.com/{{ site.admin.twitter }}">twitter.com/{{ site.admin.twitter }}</a><br />
              </p>
            </div>
          </div>
        </div>

    </body>
</html>
```

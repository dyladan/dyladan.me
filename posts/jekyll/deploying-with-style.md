# Deploying With Style

## Automating deployment with the magic of git hooks

So I know what you're thinking. Now that I've put my life changing idea into this
blog, how can I share it with the world? How do I get this goodness onto the internet?!
The answer is pretty simple actually. I used git and git hooks to automatically build
and deploy my site every time I push it to my server. Want to know how it works? Great!

So the first thing to do is to set up ssh on a server in order to use it as a git server.
I created a user specifically for git and use passwordless key authentication but
these things aren't specifically required. Once you have ssh set up the way you want it
log in to your server and create an empty git repository to push to. In this example
I assume you want to push to git@yourserver.com:awesomesite.git

```bash
$ ssh git@yourserver.com
$ mkdir awesomesite.git
$ cd awesomesite.git
$ git init --bare
```

Note: In order for this next part to work, you must have ruby and Jekyll installed
on the remote server or the site will not build.

So now that you have a git repository to push to, you need to make it build Jekyll
automatically. This is done using git hooks. Inside your git repository, there is a
directory called hooks. Inside this directory make a file called `post-receive` with
the following contents (substitute appropriate values for your `GIT_REPO` and
`PUBLIC_WWW`):

```bash
#!/bin/bash -l
GIT_REPO=$HOME/awesomesite.git
TMP_GIT_CLONE=$HOME/tmp/git/jekyll
PUBLIC_WWW=/var/www

git clone $GIT_REPO $TMP_GIT_CLONE
jekyll build --source $TMP_GIT_CLONE --destination $PUBLIC_WWW
rm -Rf $TMP_GIT_CLONE
exit
```

Now make this file executable

```bash
$ chmod +x post-receive
```

Now when you push to this server it should build your jekyll site to the `/var/www` location.
I'll leave setting up the webserver to you.

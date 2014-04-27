---
layout: post
title: "Setting up Octpress for Github Pages on Linux"
date: 2012-02-15 21:00
comments: true
categories: blog setup
---

I have used [WordPress](http://wordpress.org/) for more than 3 years. Yet recently I decided to switch to [Octopress](http://octopress.org/) for personal notes. WordPress is too heavy to use, and it needs a lot of effort on configuring, which makes me feel bad. Besides that, I need to rent a space to set things up. Octopress relies heavily on [Ruby](http://www.ruby-lang.org/en/), which is used as my main develop language these days. And Github Pages supports Octopress. Therefore you can just use Octopress as your personal blog, generate static pages and put them onto Github. This article talks about how to set things up for a Github blog.

## Ruby Environment Setup

Ruby is out of box for most Linux distributions. You can install [rvm](https://rvm.io/) to make managing various Ruby versions and environments easier.

``` bash
$ curl -L https://get.rvm.io | bash -s stable --ruby
```

Then you can list the installed Ruby versions by inputting:

``` bash
$ rvm list

rvm rubies

   ruby-1.9.2-p320 [ x86_64 ]
=* ruby-1.9.3-p194 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

Then install bundler to manage the gemsets you need for working on different machines:

``` bash
$ gem install bundler
Fetching: bundler-1.2.1.gem (100%)
Successfully installed bundler-1.2.1
1 gem installed
Installing ri documentation for bundler-1.2.1...
Installing RDoc documentation for bundler-1.2.1...
```

> Note: If you got any error here, please install **libssl-dev** package(on Debian). It's neccessary when building the openssl gem. You can install it by typing **sudo apt-get install libssl-dev** in the console.


## Basic Configuration

If you want to hold your blog with URL like this: http://your-username.github.com, you need to create a project named "your-username.github.com" on Github.

Then you need to get a copy of source of Octopress and do some basic setup:

``` bash
$ git clone https://github.com/imathis/octopress.git octopress
$ cd octopress
$ bundle update
$ rake install
$ rake setup_github_pages
```

It will do below tasks:

- Ask you for your Github Pages repository url.
- Rename the remote pointing to imathis/octopress from 'origin' to 'octopress'
- Add your Github Pages repository as the default origin remote.
- Switch the active branch from master to source.
- Configure your blog's url according to your repository.
- Setup a master branch in the _deploy directory for deployment.

When it prompts for the git repository URL, you need to provide a full URL to that git repo, like this: **git@github.com:username/username.github.com.git** (Replace `username` with actual username).


## Generate Posts and Deploy


After that, you can generate a new post like this:

``` bash
$ rake new_post\["Setting up Octpress on Github"\]
mkdir -p source/_posts
Creating new post: source/_posts/2012-02-25-setting-up-octpress-on-github.markdown
```

Then you can edit that markdown file with your favorite markdown editor(I prefer retext on Linux).

``` bash
$ retext source/_posts/2012-02-25-setting-up-octpress-on-github.markdown
```

After you have added your contents, simply run below commands:

``` bash
$ rake generate
$ rake deploy
```

It will generate the static blog pages, copy the generated files into _deploy/, add them to git, commit and push them up to the master branch.

**Don't forget to submit the markdown file to source branch.**

``` bash
$ git add .
$ git commit -m 'Publish my first blog post.'
$ git push origin source
```

After several minutes, you can open the URL *http://your-username.github.com* with your favorite browser to check if it has the content as expected.


## Others


If you want to update Octopress with latest changes from the original repo, you can do it like this:

``` bash
$ cd octopress
$ git remote add upstream https://github.com/imathis/octopress.git
$ git pull upstream
$ git push
```

An alternative way is before you do any modification after the git clone, you can run below commands:

``` bash
$ cd octopress
$ git remote rename origin upstream
```

Then you can get the latest changes by typing:

``` bash
$ cd octopress
$ git pull upstream
$ git push
```


## References

- [zhubert's Getting Started With Octopress](http://www.zhubert.com/blog/2012/04/26/getting-started-with-octopress/) on his blog.
- [Octopress' Github Deploy Guide](http://octopress.org/docs/deploying/github/)


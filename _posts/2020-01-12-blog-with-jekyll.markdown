---
layout: post
title:  "Blogging with Github Pages and Jekyll"
date:   2020-01-12 20:00:00 +0100
categories: github jekyll
---

I have decided to use a blog to store descriptions for my hobby projects. While I have a server I decided 
not to use it for blogging, because I don't want to maintain it.

I have a [Github][github] account, so I decided to use [Github Pages][github-pages] together with [Jekyll][jekyll].

Here's a description how I did it on Windows with WSL and Visual Studio Code.

# Installation #

1. Create a Github repository with the following name `https://github.com/YourUsername/YourUsername.github.io`, see also [here][github-pages].
2. Clone the repo locally to your machine.
3. Open your WSL command prompt.
4. Install Ruby development tools, if necessary, see also [here][jekyll-docs]. E.g. `sudo apt install ruby-dev`
5. Install needed gems with `sudo gem install jekyll bundler github-pages`
6. Change directory to the local copy of the repo
7. Create a new Jekyll site here with `jekyll new .`
8. I use [Visual Studio Code][vscode] with the extension [Markdown All In One][markdown-plugin]. Open directory in Visual Studio Code.
9. Add to the `Gemfile`

{% highlight Gemfile %}

source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

{% endhighlight %}

10. Customize `_config.yml`, e.g. title, description, email, etc.
11. Run `bundle install` and `bundle update`

#  Blogging #
1. To add a new blog post, add a file `2020-01-12-blogposttitle.markdown` (change accordingly) to the sub-directory `_posts`. See existing files for hints how to format.
2. Start blogging as markdown files.
3. Build the site and make it available locally with `bundle exec jekyll serve --no-watch`
4. You can now open the blog in your browser at `http://127.0.0.1:4000`.

# Publish at Github #
1. With Git Commit and Push all files to your Github repo

[github]: https://github.com/
[github-pages]: https://pages.github.com/
[jekyll]: https://jekyllrb.com/
[jekyll-docs]: https://jekyllrb.com/docs/
[vscode]: https://code.visualstudio.com/
[markdown-plugin]: https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one
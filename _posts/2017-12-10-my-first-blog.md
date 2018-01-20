---
layout: post
title: My First Blog!
tags: github jekyll
---

For a long time, I was planning to start writing Tech Blogs on technologies that I use in my day to day work but I couldn't find an easy to use tool to set up my blog. I have my portfolio website hosted on S3 which is a simple static HTML5 & CSS3 website.

So to add a blog section to it I came across this amazing tool `Jekyll` which can be used to generate blog aware static site. It uses markdown format to write blogs and creates the directory structure to replicate blog URLs.

It can be hosted on [GitHub pages](https://pages.github.com/) very easily and there is a support to CNAME the DNS with your personal domain.

[Jekyll](http://jekyllrb.com) is a static site generator, an open-source tool for creating simple yet powerful websites of all shapes and sizes. From [the project's readme](https://github.com/mojombo/jekyll/blob/master/README.markdown):

  > Jekyll is a simple, blog aware, static site generator. It takes a template directory [...] and spits out a complete, static website suitable for serving with Apache or your favorite web server. This is also the engine behind GitHub Pages, which you can use to host your project’s page or blog right here from GitHub.

## Installation

Setting up the Jekyll website for the first time is as simple as running the below 3 commands

```
$ gem install jekyll bundler
$ jekyll new my-awesome-site
$ cd my-awesome-site
~/my-awesome-site $ bundle exec jekyll serve
# => Now browse to http://localhost:4000
```
This will create the below directory structure with your default website
```
my-awesome-site/
├── 404.html
├── Gemfile
├── Gemfile.lock
├── _config.yml
├── _posts
│   └── 2018-01-20-welcome-to-jekyll.markdown
├── about.md
└── index.md
```
## Configuration

All the configuration related changes are to be made in `_config.yml` file

```
# Permalinks
#
# Use of `relative_permalinks` ensures post links from the index work properly.
permalink:           '/:categories/:year/:month/:day/:title'
#relative_permalinks: true

# Setup
title:               Nitin Nagpal
tagline:             'Tech Adventures'
description:         'Tech Blogs on DevOps & more'
url:                 http://blog.nitinnagpal.me
baseurl:             ''
paginate:            5
future:              true

# About/contact
author:
  name:              Nitin Nagpal
  url:               http://nitinnagpal.me
  email:             nitnagpal@gmail.com

github:
  repo:              https://github.com/nitnagpal/nitnagpal.github.io
# Pages
pages:
  Home: '/'
  About: '/about'
  Tags: '/tags'
```
## Layouts
All the templates for your different type of pages will be created under `_layouts` directory.

```
_layouts/
├── default.html
├── page.html
└── post.html
```

## Includes
All the things that you want to include in other pages like sidebar, header will go in `_includes` directory.
```
_includes/
├── head.html
└── sidebar.html
```

## Pages
Now to create a page (`index.html` for Homepage) add the below lines to top of the html file so that Jekyll can build the page accordingly. After that simply add all of the HTML you want to design your page.

```
---
layout: default
title: Home
---
```
* layout - the layout from `_layouts` that this page will use
* title - the page title

## Posts
All the blog posts will go under the `_posts` folder and the file name should follow a standard format so that Jekyll can extract the date of blog post from name `YYYY-MM-DD-blog-name.md`

```
_posts/
├── 2017-12-10-my-first-blog.md
└── 2017-12-17-ami-automation-using-packer.md
```

## Final Result
The final result of all the above will be that Jekyll will create a `_site` directory with your complete website in a static format and blog urls as sub-directories with html pages

```
_site/
├── 2017
│   └── 12
│       ├── 10
│       │   └── my-first-blog.html
│       └── 17
│           └── ami-automation-using-packer.html
├── 404.html
├── CNAME
├── LICENSE.md
├── README.md
├── about.html
├── atom.xml
├── feed.xml
├── fonts
│   └── font-awesome
├── index.html
├── js
│   └── custom.js
├── public
│   ├── apple-touch-icon-precomposed.png
│   ├── css
│   ├── favicon.ico
│   └── nitinnagpal.jpg
└── tags.html
```
Find out more by [visiting the project on GitHub](https://github.com/mojombo/jekyll).

---
layout: post
title: "Rails Namespaced Scaffold Generator"
date: 2014-08-23 21:29:07 +0300
comments: true
categories: ["Rails", "Scaffold", "Model", "Controller", "Generator"]
---

<h2>Introduction</h2>

When I must create a project with an admin panel, I need to do it fast and my choise for this purpose was rails' scaffold generators. But I must add an <code>:admin</code> namespace for all of my controllers for the admin panel. Rails' default scaffold generator has not a feature like that. So I find a solution with <a href="https://github.com/dhampik/rails-admin-scaffold">rails admin scaffold</a> gem. This gem is simply make a namespace for scaffold controllers and organize urls and paths accordingly.

<!-- more -->

<h2>Install</h2>

Add <a href="https://github.com/dhampik/rails-admin-scaffold">rails-admin-scaffold</a> gem to gemfile of project

``` ruby
gem 'rails-admin-scaffold'
```

After that, we must generate our model and it's admin namespaced controller separately.

``` bash
rails g model Comment title:string description:text
```

``` bash
rails g admin:scaffold_controller Comment title:string description:text
```

You can also determine parent controller with <code>--parent_controller</code> option like that;

``` bash
rails g admin:scaffold_controller Comment title:string description:text --parent_controller=admin
```

This command will generate an admin namespaced comment controller that inherit from admin controller.
<code>class Admin::CommentsController < AdminController</code>

<h2>Conclusion</h2>

This simple gem is a time saver when you want to use rails' scaffold generator for an admin panel. It has Rails 4 support and haml, jbuilder options. I wish you can save time with this gem in your next project :)

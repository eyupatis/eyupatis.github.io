---
layout: post
title: "Setup Octopress Blog And Deploy To Heroku"
date: 2014-03-26 00:53:07 +0200
comments: true
sharing: true
categories: [Octopress, Blogging, Heroku]
---

<h2>Initial Setup</h2>
<p>I created this blog site with <a href="http://octopress.org/">octopress</a>. Formerly, I was using blogspot but I want to change a flexible platform like jekyll based octopress. Here is how I setup this blog site from scratch.</p>
<!-- more -->

<h3>Install Git</h3>
<p>Firstly, you must have to install <a href="http://git-scm.com/">git</a></p>

<h3>Install RVM, Ruby and Bundler</h3>
<p>You must run the following command for installing rvm</p>

{% highlight bash %}
curl -L https://get.rvm.io | bash -s stable --ruby
{% endhighlight %}

<p>After the rmv install properly, we need to install ruby</p>
{% highlight bash %}
rvm install 2.1.1
rvm use 2.1.1 --default
rvm rubygems latest
{% endhighlight %}

<p>Install Bundler gem</p>
{% highlight bash %}
gem install bundler
{% endhighlight %}

<h3>Install Octopress</h3>
<p>We will clone the repository and make our changes directly in the octopress source codes.</p>
{% highlight bash %}
git clone git://github.com/imathis/octopress.git octopress
cd octopress
{% endhighlight %}

<p>Inside the project, you must run bundle for installing dependencies of octopress gem.</p>
{% highlight bash %}
bundle
{% endhighlight %}

<h2>Deploying to Heroku</h2>
<p>You must install heroku gem first</p>
{% highlight bash %}
gem install heroku
{% endhighlight %}

<p>You must have a heroku account. After the sign in you can open a new app from <a href="https://dashboard.heroku.com/apps">heroku dashboard</a></p>
<p>Now, you must add a remote address to your project. For that purpose, go to settings page of your app</p>

{% highlight bash %}
https://dashboard.heroku.com/apps/yourapp/settings
{% endhighlight %}

<p>Copy the Git Url of your app and run the following command</p>

{% highlight bash %}
git remote add heroku git@heroku.com:yourapp.git
{% endhighlight %}

<p>Edit the <code>.gitignore</code> in the root of your repository and remove <code>public</code>. This will let you add generated content for deploying it to Heroku.</p>
<p>Let's build the static contents and deploy them to heroku</p>
{% highlight bash %}
rake generate
git commit -am "initial commit"
git push heroku master
{% endhighlight %}

<p>Now you can view your blog site from <code>yourapp.herokuapp.com</code></p>

<h2>Customize Theme</h2>
<p>I does note like the default theme of octopress and want to switch another theme. <a href="https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes">Here</a> you can find a list of available themes. This blog uses <a href="https://github.com/tommy351/Octopress-Theme-Slash">Slash</a> theme.</p>

<p>We must clone theme from the repository and install to our blog</p>

{% highlight bash %}
cd octopress
git clone git://github.com/tommy351/Octopress-Theme-Slash.git .themes/slash
rake install['slash'] 					# if you are using zsh you must install like this rake install\['slash'\]
rake generate
{% endhighlight %}

<p>Of course after these changes, you must commit and push your works to heroku.</p>

<p>Now you have a running blog site. You can find the detailed information of how to write your first blog post from <a href="http://octopress.org/docs/blogging/">octopress-blogging</a> and <a href="http://jekyllrb.com/docs/posts/">jekyll-writing posts</a></p>

<p>Good luck!</p>

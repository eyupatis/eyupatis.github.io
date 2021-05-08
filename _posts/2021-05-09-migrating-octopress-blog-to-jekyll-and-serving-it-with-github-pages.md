---
title: Migrating Octopress Blog to Jekyll and Serving It with Github Pages
---

Hello everyone,

In the past, I've built my personal blog with [Octopress](http://octopress.org/). It was a very good option at that point, but after some time, project is abandoned and not maintained well.

I wanted to switch my blog from Octopress to Jekyll. Because my blog was very simple and it only included a few posts, migration process was easy as well.

I started with installing [Jekyll](https://jekyllrb.com/) by:

```bash
gem install bundler jekyll
jekyll new eyupatis.github.io
```

After that, because I already had a url structure on `octopress`, I had to copy the same configuration to `_config.yml` file.

```yaml
permalink: /:title/
```

For making google analytics works same with the old blog, I had to copy that configuration as well:

```yaml
google_analytics: UA-XXXXXXXXXX
```

I manually copied my old posts under `/source/_posts` to `/_posts` folder. Because octopress was using an old plugin called `code_block`, in my old posts, I was referring to the file names while sharing code samples like `ruby User.rb`. This functionality was causing some problems, so I had to delete the file names from code samples. I need to find a maintained version of a similar plugin to add the same functionality to Jekyll.

At this point, I controlled the changes locally with `bundle exec jekyll serve` command and everything seemed fine.

The next step was deploying changes to Github. Because I created a totally new project, I had to define the remote url first.

```bash
git remote add origin git@github.com:eyupatis/eyupatis.github.io.git
```

We need to push to a special branch called `gh-pages`. After pushing the changes to this branch, I could load the new changes on [eyupatis.github.io](http://eyupatis.github.io/).

I also configured my blog to be redirected to [eyupatis.com](https://www.eyupatis.com/) like following:

![]({{ 'assets/images/Screen Shot 2021-05-09 at 01.22.58.png' | relative_url }})

This was the end of the journey. I hope you can migrate your octopress blog to Jekyll with ease by following this post.

See you next time!

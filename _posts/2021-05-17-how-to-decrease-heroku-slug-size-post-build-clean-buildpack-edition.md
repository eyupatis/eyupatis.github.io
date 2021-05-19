---
title: 'How to Decrease Heroku Slug Size: Post Build Clean Buildpack Edition'
---

**Note:** You can read the wkhtmltopdf edition of "How to Decrease Heroku Slug Size" series [here]({% post_url 2021-05-17-how-to-decrease-heroku-slug-size-wkhtmltopdf-edition %}).

Heroku is a platform as a service tool where you can host your websites easily. It is helping the teams who don't want to maintain their own servers. 

Even though Heroku is a very helpful platform for the development teams, sometimes it can introduce limitations related to how you can serve your applications. In this post, we will mention the **slug size** limitation of Heroku. 

Heroku wants our projects to have small sizes in order to have a faster CI/CD and boot times. Compiling and deploying a project with small size is way faster than having a big one. Once you hit **300MB** limit on your slug, Heroku starts warning you about the problems related to having a big slug size. The [current](https://devcenter.heroku.com/changelog-items/1145) hard limitation on Heroku is **500MB**.
## Beginning

![]({{ 'assets/images/Screen Shot 2021-05-17 at 16.12.06.png' | relative_url }}) 

Our project was very close to the hard limitation of Heroku at the beginning as you can see from the image. When I connected to our application dyno with `heroku run bash` command, I wanted to see what are the biggest files are. I accomplished this with `du -h | sort -rh` command. 

![]({{ 'assets/images/Screen Shot 2021-05-19 at 09.55.34.png' | relative_url }})

In the `matching_inbox` directory, we have our Ember application.  As you can see from the image, the uncompressed version of `node_modules` folder was taking **400MB** of space. 
## What ember-cli-rails does?
Ember-cli-rails gem has multiple features, but it this post we will focus on having a single way to deploy our application with `git push` command. You can see the other features [here](https://github.com/thoughtbot/ember-cli-rails#ember-cli-rails). 

When you have `ember-cli-rails` in your project, you can use the same way of deploying applications to Heroku. There are multiple ways to deploy an application to Heroku but the most common way is through git pushes. While heroku is compiling the application, it is also running `ember build` command to build the ember application and stores the builded application in temporary folders. After the ember application is built and stored, you don't actually need to have `node_modules` folder anymore.

While investigating this issue further, I encountered with an existing github issue on `ember-cli-rails` repo as [Heroku Slug Size: can node_modules be deleted after build](https://github.com/thoughtbot/ember-cli-rails/issues/491). In this issue developers shared some helpful messages which I used in this blog post as well. 
## Setting up heroku-buildpack-post-build-clean
You can add buildpack with `heroku buildpacks:add --index 1 https://github.com/Lostmyname/heroku-buildpack-post-build-clean` command. You may need to adjust `--index` parameter depending on the existing buildpacks on your application. After that, we need to define a new file called `.slug-post-clean`. This file should have the ember application path and node modules and tmp folders as: 

```
matching_inbox/node_modules
matching_inbox/tmp
```
## Result
When we deploy the changes to our application, we could see a decrease on slug size but it was not enough for us to stop here.

![]({{ 'assets/images/Screen Shot 2021-05-17 at 16.46.06.png' | relative_url }})

We decreased Heroku slug size further with the changes to `wkhtmltopdf` set-up and you can read that post here: [How to Decrease Heroku Slug Size: WKHTMLTOPDF Edition]({% post_url 2021-05-17-how-to-decrease-heroku-slug-size-wkhtmltopdf-edition %}).

Thank you for your time, I hope it can help you as well.

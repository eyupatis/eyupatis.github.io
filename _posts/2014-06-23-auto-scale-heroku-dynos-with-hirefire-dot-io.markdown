---
layout: post
title: "Auto Scale Heroku Dynos With Hirefire.io"
date: 2014-06-23 10:41:36 +0300
comments: true
categories: [Heroku, Deployment, Performance]
---

<a href="http://www.hirefire.io">Hirefire.io</a> is a service that auto scales your heroku dynos. It is running both web and worker dynos and scales them according to websites current respond times and non-processed jobs. The setup of this tool is very easy.

<!-- more -->

If you just want to scale your web dynos you no need to any code changes in your app. You can just sign up to hirefire and make configurations of your website.

If you interested with scaling your worker dynos you must add some codes for counting your non-completed job in the queues. I will explain configuration of <code>Sidekiq</code> gem. You can find more worker gem configurations from <a href="http://hirefire.io/documentation/guides/macros">here</a>.

Firstly, we must add <code>hirefire-resource</code> gem to our <code>Gemfile</code>

``` ruby
gem "hirefire-resource"
```

After <code>bundle</code> command, we must configure hirefire for watching our queue.

``` ruby
HireFire::Resource.configure do |config|
  config.dyno(:worker) do
    HireFire::Macro::Sidekiq.queue
  end
end
```

<code>config.dyno(:worker_dyno_name)</code> must be equal to your worker dyno name in <code>Procfile</code>


When you visit <code>http://localhost:3000/hirefire/development/info</code> you must see something like this:

``` json
[{"name": "worker", "quantity": 132}]
```

When you deploy and configure your account from hirefire you can auto scale your dynos.

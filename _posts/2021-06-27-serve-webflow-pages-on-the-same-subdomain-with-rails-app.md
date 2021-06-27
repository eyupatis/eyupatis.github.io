---
title: Serve Webflow Pages On The Same Subdomain With Rails App
---

## Background
Webflow is a no-code web design and development platform. You can use it to serve your static pages like marketing and landing pages. 

Most of the CMS tools are designed to own a subdomain completely.  A common DNS setting that you can see on the internet is serving CMS pages on `www` subdomain and serving application URLs on `app` subdomain.
## Goal
Our primary goal was to keep all of the pages on `www` subdomain. We wanted to also remove static pages like `/terms-of-use` or `/about-us` from our Rails application's source code and move them to Webflow. Another goal was serving `/blog/*` pages on the same subdomain so that we can get a better SEO boost from the blog posts.

The end goal was achieving the serving map that is described in the next table:

| URL | Page Name | Served By |
| -------- | -------- | -------- |
| http://www.example.com | HomePage | Rails |
| http://www.example.com/about-us | About Us | Webflow |
| http://www.example.com/blog/posts/1 | Blog Post | Webflow |
| http://www.example.com/login | Login | Rails |
| http://www.example.com/sign_up | Sign Up | Rails |

## Configuring Webflow
We need to configure Webflow so that it starts serving pages from a subdomain. To do that, after clicking the settings of the project from the dashboard, we need to go to the hosting section and under the Custom Domains section, we need to add a subdomain as `webflow.example.com`. 

![]({{ 'assets/images/Screen Shot 2021-06-27 at 11.13.42.png' | relative_url }})
![]({{ 'assets/images/Screen Shot 2021-06-27 at 11.14.34.png' | relative_url }})
![]({{ 'assets/images/Screen Shot 2021-06-27 at 11.14.59.png' | relative_url }})
## Configuring DNS
Now, we need to start proxying the requests that come to `webflow.example.com` to Webflow. To do that, we need to add a `CNAME` record to our DNS records. The name/value should be `webflow` and the target should be `proxy-ssl.webflow.com`. If you are using CloudFlare as your DNS provider, you should disable `Proxied` option and set it to `DNS Only`. 

![]({{ 'assets/images/Screen Shot 2021-06-27 at 11.16.15.png' | relative_url }})
## Configuring Rails
The last step is making changes to the Rails application so that when we get a request to `/about-us` page, we can reverse proxy the request to `webflow.example.com/about-us`. 

We need to introduce [rails-reverse-proxy](https://github.com/axsuul/rails-reverse-proxy) gem to our application. After adding the gem to `Gemfile` we need to run `bundle` command from the terminal to install gem to our project.

We also need to introduce a new route and controller to handle requests. You can add the following code to the very end of the `routes.rb` file.

```ruby
match '/*path' => 'webflow#index', via: [:get, :post, :put, :patch, :delete], constraints: lambda { |req|
  req.path.exclude? 'rails/active_storage'
}
```

This code matches all the URLs that are not defined previously in your `routes.rb` file and send them to `webflow` controller's `index` action. It has an exception related to `active_storage`, because if you are using `active_storage` gem in your application, you are not defining any routes for it in `routes.rb` file so the requests to `active_storage` attachments won't be caught by any other line of code than our new configuration. 

We need to also create `webflow_controller.rb` as:

```ruby
class WebflowController < ApplicationController
  include ReverseProxy::Controller

  def index
    reverse_proxy ENV['WEBFLOW_URL'], path: params[:path] do |config|
      config.on_missing do
        render file: Rails.root.join('public', '404.html'), status: :not_found && return
      end
    end
  end
end
```

Here, we are calling `reverse_proxy` method of the gem and getting HTML responses from Webflow, and serving them on our own Rails application. `rails-reverse-proxy` gem is also returning us HTTP statuses like `404 Not Found` `500 Internal Server Error` and we can handle those requests on callbacks like `on_missing, on_error`.

`WEBFLOW_URL` environment variable should be equal to `https://webflow.example.com/`. `:path` parameter will be equal to `about-us` or `blog/posts/1` depending on the requested URL. 

I hope this post helps the internet to have less applications that are served on `app` subdomain :)
See you next time!

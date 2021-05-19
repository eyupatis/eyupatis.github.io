---
title: 'How to Decrease Heroku Slug Size: WKHTMLTOPDF Edition'
---

**Note:** You can read the post build clean buildpack edition of "How to Decrease Heroku Slug Size" series [here]({% post_url 2021-05-17-how-to-decrease-heroku-slug-size-post-build-clean-buildpack-edition %}).
## tl;dr
Use `wkhtmltopdf-heroku` instead of `wkhtmltopdf-binary` on production environment. You can still use `wkhtmltopdf-binary` in local environment.
## Beginning
![]({{ 'assets/images/Screen Shot 2021-05-17 at 16.46.06.png' | relative_url }})

Our aim was decreasing the Heroku slug size further, after decreasing it with post build clean buildpack, as I shared in another blog post. After investigating the sizes on our dyno, I saw that `wkhtmltopdf` binaries are taking a lot of space. I wanted to learn more about the alternative ways of installing `wkhtmltopdf` on Heroku and encountered with this amazing blog post called [wkhtmltopdf on Heroku: evaluating different installation options](https://razorjack.net/wkhtmltopdf-on-heroku-evaluating-different-installation-options). I wanted to follow the last way of having `wkhtmltopdf` on Heroku and I'll share the changes that I did in our application.

![]({{ 'assets/images/Screen Shot 2021-05-17 at 15.54.28.png' | relative_url }})
## Required Changes
We are using `wicked_pdf` gem to generate pdfs from html in our application. We were also using `wkhtmltopdf-binary` to provide necessary binares  on both local environment and on production. `wkhtmltopdf-binary` gem provides binaries for OS X, Linux and Windows and that's why it's size is huge and it is not playing well with Heroku slug size limitation. In order to fix this problem on Heroku, I did the following changes to Gemfile:

```ruby
gem 'wkhtmltopdf-binary', group: [:development, :test]
gem 'wkhtmltopdf-heroku', group: :production
```

Although `wkhtmltopdf-heroku` gem is pre-configured to be used with `wicked_pdf` gem, I needed to update the `config/initializers/wicked_pdf` file, because we had a custom layout defined.

```ruby
WickedPdf.config ||= {}
WickedPdf.config.merge!({
  layout: 'pdf.html'
})
```
## Happy End
After deploying the changes to Heroku, we saw a great decrease in slug size.

![]({{ 'assets/images/Screen Shot 2021-05-17 at 16.12.32.png' | relative_url }})

Thank you for reading this post, I hope it helps you as well.

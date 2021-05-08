---
layout: post
title: "Full Text Search With Sunspot and Mongoid in Ruby on Rails"
date: 2014-12-09 20:04:46 +0200
comments: true
categories: [Mongoid, Sunspot, Full Text Search]
---

<h1>Introduction</h1>

In this tutorial, we will implement a full text searching functionality with <a href="http://sunspot.github.io/">sunspot</a> gem. Sunspot is a <a href="http://lucene.apache.org/solr/">Solr</a> powered search engine for ruby based projects. ORM of the tutorial is <a href="http://mongoid.org/en/mongoid/index.html">Mongoid</a>.

<!-- more -->

<h1>Implementation</h1>

First of all, we must add necessary gems to gemfile

``` ruby
group :development
  gem 'sunspot_solr'
end

gem 'sunspot_rails'
gem 'sunspot_mongoid'
```

`sunspot_solr` gem is added to development group because it will run only in development environment.

Now, we can run bundle command:

``` bash
bundle install
```

We can use the generator of the sunpot gem for creating configuration file.

``` bash
rails generate sunspot_rails:install
```

Then we can run Solr server:

``` bash
bundle exec rake sunspot:solr:start
```

After these steps, our project now ready for implementation of searching functionality

<h1>Model Implementation</h1>

In the model of our project we will implement necessary steps for searching.

``` ruby
class Place
  include Mongoid::Document
  include Sunspot::Mongoid

  field :title
  field :address
  field :description

  belongs_to :category

  searchable do
    text :title, boost: 4
    text :address, :description, :category_name
  end

  def category_name
    category.name
  end
end
```

We have fields like `:title, :address, :description` and one more searchable attribute as `:category_name`. We boosted `:title` field because we want to find more related searches in this field. And we searched our model with it's association's attribute. We created a method called `category_name` and we added it to searchable block.

<h2>Indexing Records</h2>
After initial implementation in the model layer, we have not indexes for doing searches properly. So we need to index all of our records according to searchable attributes. For doing this you can run rails console and index all records.
``` ruby
Sunspot.index(Place.all)
```
*There are another solutions for indexing the data but this one was enough for me.

<h1>Controller Implementation</h1>

We implemented our model and now we can search through them.

``` ruby
def search
  @search = Place.solr_search do
    fulltext params[:q]
  end

  @places = @search.results
end
```

In this search method, we called a `solr_search` method on `Place` model and passed `params[:q]` to with fulltext method. Notice that we used `solr_search` method instead of `search`, because mongoid has also a method named `search` and they are conflicting in the Mongoid case.

<h1>Conclusion</h1>

We implemented a basic full text search functionality. Sunspot gem has advance configurations for more complicated situations and needs. If you find this useful, share it for other people.

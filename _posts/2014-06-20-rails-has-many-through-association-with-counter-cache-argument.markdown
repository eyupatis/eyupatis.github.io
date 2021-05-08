---
layout: post
title: "Rails has_many through association with counter cache argument"
date: 2014-06-20 11:12:34 +0300
comments: true
sharing: true
categories: [Rails, ActiveRecord, Associations]
---

Counter cache argument prevents you from making unnecessary `SELECT COUNT (*) requests.

We want to make unnecessary `@course.users` call for each course.

Next association is a basic example for <code>:has_many, :through</code> association.

<!-- more -->

``` ruby
has_many :subscriptions
has_many :users, through: :subsriptions
```

``` ruby
has_many :subscriptions
has_many :courses, through: :subsriptions
```

``` ruby
belongs_to :user
belongs_to :course
```

We must add a counter cache for course, as users_count


``` ruby
belongs_to :user
belongs_to :course, counter_cache: :users_count
```

After that we must add a field to Course model for storing users_count

``` bash
rails g migration add_users_count_to_courses users_count:integer
```

Open the created migration file and set default value to 0 and prevent from nil records

``` ruby
class AddUsersCountToCourses < ActiveRecord::Migration
  def change
    add_column :courses, :users_count, :integer, default: 0, null: false
  end
end
```

After this step, `@course.users` will return 0, because we set the defaut value to it.

For fix this situation we must generate another migration file and fill `@course.users_count` field with correct values

``` bash
rails g migration cache_course_users_count
```

Open the file and fill :users_count field

``` ruby
class CacheCourseUsersCount < ActiveRecord::Migration
  def up
    Course.find_each do |course|
      users_count = Subscription.where(course_id: course.id).size
      course.update_attributes(users_count: users_count)
    end
  end

  def down
  end
end
```

That's all. Now you are storing `users_count` in database and cache it, so performance of your app increased.

Good for you!

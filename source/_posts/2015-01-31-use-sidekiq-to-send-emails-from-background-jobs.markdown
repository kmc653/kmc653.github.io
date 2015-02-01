---
layout: post
title: "Use Sidekiq to Send Emails from Background Jobs"
date: 2015-01-31 15:41:25 +0800
comments: true
category: ruby on rails
---
In Rails application, we usually send emails to notify users of something. Sending emails would not be fast because it need to rely on third party service ( e.q., Google, Yahoo, Mailgun ). We can put sending action in background process to make sure that application won't be held up.
#Sidekiq
We can use sidekiq to make the jobs running in the background. It will avoid our app getting stuck if one of the processes broke. 

At first, add gem `sidekiq` in `Gemfile`, and then run `bundle install` from command line
```ruby Gemfile
gem 'sidekiq'
```
For instance, if we want to send an invitation email to user, the code should be
```ruby
AppMailer.send_invitation_email(@invitation).deliver
```

Using `sidekiq`, we add `delay` to make sending action processing in background.
```ruby
AppMailer.delay.send_invitation_email(@invitation)
```

We have to install `redis` server if we would like to set up background job processing locally. we can get Redis from here: http://redis.io/download . 

Typing `redis-server` to run redis, and then type `bundle exec sidekiq` on application to execute sidekiq, and then run Rails applicaion server to send emails.
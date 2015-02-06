---
layout: post
title: "Use unicorn as production server to implement concurrent requests"
date: 2015-02-05 10:33:14 +0800
comments: true
category: ruby on rails, heroku
---
It'll be much efficient use of dyno if a web application that process requests concurrently, instead of process one request at a time. Therefore, we should implement concurrent requests in developing and running production services. Using [Unicorn](https://devcenter.heroku.com/articles/rails-unicorn) as web server let Rails applications run concurrently by running multiple Ruby processes in a single dyno.

#Install unicorn & sidekiq gems
At first, add `unicorn` and `sidekiq`in Gemfile, and then run `bundle install`.
```ruby Gemfile
gem 'sidekiq'
gem 'unicorn'
```
#Apply `Redis To Go` on Heroku
We should apply free [Redis To Go](https://addons.heroku.com/redistogo) service on heroku to work with sidekiq to make jobs running in the background.

#Create a unicorn.rb file
Add a `unicorn.rb` file in `config`:
```ruby unicorn.rb
worker_processes Integer(ENV["WEB_CONCURRENCY"] || 3)
timeout 15
preload_app true

before_fork do |server, worker|
  Signal.trap 'TERM' do
    puts 'Unicorn master intercepting TERM and sending myself QUIT instead'
    Process.kill 'QUIT', Process.pid
  end

  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.connection.disconnect!
end

after_fork do |server, worker|
  Sidekiq.configure_client do |config|
    config.redis = { :size => 1 }
  end
  Sidekiq.configure_server do |config|
    config.redis = { :size => 5 }
  end

  Signal.trap 'TERM' do
    puts 'Unicorn worker intercepting TERM and doing nothing. Wait for master to send QUIT'
  end

  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.establish_connection
end
```

And then, add two lines of command in `Procfile`:
```ruby Procfile
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
worker: bundle exec sidekiq -e production -C
```

Carefully, in above setting, there are two dynos, one of them is web dyno, another one is worker dyno. However, in heroku, we have to swipe the credit card if we use more than one dyno.

Therefore, if we just want to have a taste and don't want to pay money, add a line of code in `unicorn.rb`:
```ruby unicorn.rb
worker_processes Integer(ENV["WEB_CONCURRENCY"] || 3)
timeout 15
preload_app true

before_fork do |server, worker|
  @sidekiq_pid ||= spawn("bundle exec sidekiq -c 2")  # <== this line
  Signal.trap 'TERM' do
    puts 'Unicorn master intercepting TERM and sending myself QUIT instead'
    Process.kill 'QUIT', Process.pid
  end

  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.connection.disconnect!
end

after_fork do |server, worker|
  Sidekiq.configure_client do |config|
    config.redis = { :size => 1 }
  end
  Sidekiq.configure_server do |config|
    config.redis = { :size => 5 }
  end

  Signal.trap 'TERM' do
    puts 'Unicorn worker intercepting TERM and doing nothing. Wait for master to send QUIT'
  end

  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.establish_connection
end
```

This line of code means sidekiq workers will be spawned from unicorn within web dyno.

And the `Procfile` should only be:
```ruby Procfile
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
```

However, building a real production Rails application, we're not according to the above steps, these just give you a try without pulling out the credit card.
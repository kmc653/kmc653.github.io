---
layout: post
title: "use Paratrooper gem to automate the deploy pipeline"
date: 2015-02-15 16:03:19 +0800
comments: true
category: ruby on rails, heroku 
---
#Paratrooper
Every time we deploy a rails applicaton to staging server or production server, there will be some steps we need to process ( Turn maintenance page on, git push changes to heroku, run database migrations, turn off maintenance page ). Using `paratrooper` gem  help us to simplify the process. We're able to deploy our application to the staging server with simply type command `rake deploy:staging`, and deploy to the production server with command `rake deploy:production`.

At first, add `paratrooper` in Gemfile, and then run `bundle install`.
```ruby Gemfile
gem 'paratrooper'
```

Add a file named `deploy.rake` and placed it in the lib/tasks directory. Here is what it looks like:
```ruby deploy.rake
 require 'paratrooper'

 namespace :deploy do
   desc 'Deploy app in staging environment'
   task :staging do
     deployment = Paratrooper::Deploy.new("amazing-staging-app", tag: 'staging')

     deployment.deploy
   end

   desc 'Deploy app in production environment'
   task :production do
     deployment = Paratrooper::Deploy.new("amazing-production-app") do |deploy|
       deploy.tag              = 'production',
       deploy.match_tag        = 'staging',
       deploy.maintenance_mode = !ENV['NO_MAINTENANCE']
     end

     deployment.deploy
   end
 end
```

In above, we should modify code from `amazing-staging-app` and `amazing-production-app` to our staging and production applications' name.

It's also important to note that in the line 15 of `deploy.rake` file, there's a `match_tag` equals `staging`, which means we're able to deploy to production server only if the application has been pushed and tested on the staging. This insures that deploying to staging is not to be omitted. 
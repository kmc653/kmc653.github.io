---
layout: post
title: "Procfile and Foreman"
date: 2015-02-01 17:39:07 +0800
comments: true
categories: ruby on rails, heroku 
---
#Procfile
`Procfile` is a text file, placed in the root of the Rails application, used to declare what commands are run by dynos of the application on the Heroku. We can use Procfile to declare different kind of process type. Each process type is a declaration of command that is executed when a dyno of that process type is started.

The following code is the format of process type, one process per line.
```ruby
<process type>: <command>
```

For example,
```ruby
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
```

#Foreman
When we develope and debug an applicaion, the local developement environment is as similar as remote environments will avoid us from expending extra time to fix errors. `Foreman` is a command-line tool for running `Procfile` to execute application, which would make local emvironment works like remote's.

Type `foreman start` in command-line to execute `Foreman`
```
$ foreman start
21:12:35 web.1  | started with pid 25658
21:12:36 web.1  | I, [2015-02-01T21:12:36.014308 #25658]  INFO -- : Refreshing Gem list
21:12:38 web.1  | I, [2015-02-01T21:12:38.087552 #25658]  INFO -- : listening on addr=0.0.0.0:5000 fd=10
21:12:38 web.1  | I, [2015-02-01T21:12:38.256511 #25658]  INFO -- : master process ready
```
We can see the application running on the http://localhost:5000
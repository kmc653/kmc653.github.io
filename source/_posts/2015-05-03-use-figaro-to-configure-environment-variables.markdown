---
layout: post
title: "Use Figaro to Configure Environment Variables"
date: 2015-05-03 12:00:18 +0800
comments: true
categories: 
---
In developing web application, we must have some sensitive information that should be hidden. Using `figaro`, which place sensitive information in a Git-ignored YAML file, can offer this service for us.

#Install Figaro gem
Add `figaro` gem in the Gemfile and run `bundle install`

    gem "fiagro"

And then run installation:

    $ figaro install

This command will create a `application.yml` file in `config`, and this yml file will also be added in `.gitignore`. We can place our own configuration in here:
```ruby application.yml
development:
  AWS_ACCESS_KEY_ID: xxxxxxxxxx
  AWS_SECRET_ACCESS_KEY: xxxxxxxxxx
  S3_BUCKET_NAME: xxxxxxxxxx

test:
  AWS_ACCESS_KEY_ID: xxxxxxxxxx
  AWS_SECRET_ACCESS_KEY: xxxxxxxxxx
  S3_BUCKET_NAME: xxxxxxxxxx

production:
  AWS_ACCESS_KEY_ID: xxxxxxxxxx
  AWS_SECRET_ACCESS_KEY: xxxxxxxxxx
  S3_BUCKET_NAME: xxxxxxxxxx
```
For example, if you want to get `S3_BUCKET_NAME`, you use `ENV['S3_BUCKET_NAME']`.

#Set Configuration to Heroku
`figaro` offer an easy way to set remote Heroku configuration:

    $ figaro heroku:set -e production

This command will set environment variables to your application on the Heroku.

By the way, the environment option for the `heroku:set` is required. In above example, the environment is set to `production`. The default will be `development` if unspecified.

Reference from [laserlemon/figaro](https://github.com/laserlemon/figaro)
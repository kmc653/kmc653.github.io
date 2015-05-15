---
layout: post
title: "Set up a staging server on heroku"
date: 2015-02-07 21:37:37 +0800
comments: true
category: ruby on rails, heroku 
---
When developing an application, we always meet a situation is that new features work well in local machine, but break in production server. Hence, it will be better if we can test out new features in a "production like" environment. The solution is to create a staging server that is similar to production server. We are able to test new functions of the application on staging environment, and then deploy to production environment if everything works well. In the following, I'll introduce how to set up a staging server on heroku when already having a production one.

First of all, in convention, the staging server will be named to have `staging` word. For example, if the application on heroku named `wordsfarm`, the staging server can be named `wordsfarm-staging`.
```
$ heroku fork -a wordsfarm wordsfarm-staging
```
This `fork` command does a lot of things. It copies the config vars, data in Postgres and re-provision all add-ons on heroku. 

And then, we should add a new remote by using `git remote add` command:
```
$ git remote add wordsfarm-staging git@heroku.com:wordsfarm-staging.git
```
We can use `git remote -v` command to show all remote connections with URL.

    heroku  https://git.heroku.com/wordsfarm.git (fetch)
    heroku  https://git.heroku.com/wordsfarm.git (push)
    origin  https://github.com/kmc653/wordsfarm.git (fetch)
    origin  https://github.com/kmc653/wordsfarm.git (push)
    wordsfarm-staging git@heroku.com:wordsfarm-staging.git (fetch)
    wordsfarm-staging git@heroku.com:wordsfarm-staging.git (push)

After doing these, we can see there is a new application on heroku named `wordsfarm-staging` with independent database and Add-ons. Therefore, we can test on staging server to keep changes from breaking things on production server. 

If we want to deploy to the staging server, we should: 
```
$ git push wordsfarm-staging master
```
---
layout: post
title: "Use Concerns to DRY up Model"
date: 2015-01-29 19:20:35 +0800
comments: true
categories: Ruby on Rails
---
Different models in Rails application would have the code that have the same logic or functionality. Using `concerns` to put these same code in a module, which would allow you to use in any models.
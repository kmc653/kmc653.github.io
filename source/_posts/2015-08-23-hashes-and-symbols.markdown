---
layout: post
title: "Hashes and Symbols in Ruby"
date: 2015-08-23 16:18:49 +0800
comments: true
category: Ruby 
---
#Hashes
Ruby hashes are collections of key-value pairs. The key is unique and is associated with some value. For example:

    foods = {
      "American" => "hamburger",
      "Italian" => "spaghetti",
      "Chinese" => "dumpling"
    }

We also can use `symbol` to be the key:

    foods = {
      :American => "hamburger",
      :Italian => "spaghetti",
      :Chinese => "dumpling"
    }

It also can be represented like this without hash rocket `=>` after Ruby 1.9 version:
    
    foods = {
      American: "hamburger",
      Italian: "spaghetti",
      Chinese: "dumpling"
    }
#Symbols
The key bahavior of `symbol` is that there's only one copy of any particular symbol at a given time, which is different from `string`. For example:

    puts "queens".object_id
    puts "queens".object_id

    puts :queens.object_id
    puts :queens.object_id

    => 70182068437220
       70182068393120
       538248
       538248

#Converting between symbols and strings
We can use `to_s` and `to_sym` to convert between strings and symbols. For example:

    :NYPD.to_s => "NYPD"
    "NYPD".to_sym => :NYPD

Reference from [Codecademy](http://www.codecademy.com) ruby course.
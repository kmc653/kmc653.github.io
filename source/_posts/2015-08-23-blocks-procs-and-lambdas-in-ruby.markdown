---
layout: post
title: "Blocks, Procs, and Lambdas in Ruby"
date: 2015-08-23 18:25:35 +0800
comments: true
category: Ruby 
---
You must heard that "Ererything is an object in Ruby" when you start to learn Ruby. However, `Blocks` are not objects. Because of this, blocks can't have any abilities like objects unless using `procs`. 

#Proc
We can name a block of code and turn it into a method by using `procs`, which means we can call it over and over without retype the block. This could avoid us from repeating codes.

For example,

    choose_even = Proc.new do |n|
      n.even?
    end

    (1..10).to_a.select(&choose_even)   => [2, 4, 6, 8, 10]

Some methods, like `.map`, `.collect` and `.select`, normally take a block, so the `&` is used to convert proc into a block.

    square = Proc.new { |x| x ** 2 }
    
    [1, 2, 3].map(&square)    => [1, 4, 9]

We can user `.call` method to call proc directly.

    test = Proc.new { puts "Hello!"}
    test.call

    => Hello!

By the way, we can pass ruby method name with a symbol.

    n_array = [1, 2, 3, 4, 5]
    s_array = n_array.map(&:to_s)   => ["1", "2", "3", "4", "5"]

#Lambda
`Lambda` is almost have the same behavior with `Proc`. `lambda { puts "Awesome!" }` is the same as `Proc.new { puts "Awesome!" }`.

    movies = ["spyder man", "bat man", "iron man"]
    symbolize = lambda { |x| x.to_sym }

    symbols = movies.collect(&symbolize)  => [:"spyder man", :"bat man", :"iron man"]

The difference between `Proc` and `Lambda` is that: 

1. lambda will check the number of arguments passed to it. It will throw an error if we pass the wrong number of arguments. Whereas a proc will ignore unexpected arguments and assign `nil` to any missing.

2. When a lambda returns, it will go back to the calling method. However, proc won't go back to the calling method.


Reference from [Codecademy](http://www.codecademy.com) ruby course.
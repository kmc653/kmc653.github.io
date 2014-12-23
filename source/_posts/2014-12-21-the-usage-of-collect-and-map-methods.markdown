---
layout: post
title: "The Usage of Collect &amp; Map Methods"
date: 2014-12-21 22:57:15 +0800
comments: true
categories: Ruby
author: kmc653
---
#Collect
* _collect { | item | block } => array_
* _collect => Enumerator_  

The method `collect` is used in an array. Returns a new array with the results of executing block once for each element in array.  
If no block is given, an enummerator is returned instead.

    ['a', 'b', 'c'].collect{ |letter| letter.capitalize }     #=> ["A", "B", "C"]
There is a concise way doing the same work:

    ['a', 'b', 'c'].collect(&:capitalize)     #=> ["A", "B", "C"]
The element will be replaced with the value returned by block if adding a `!`

    a = ['a', 'b', 'c'].collect!(&:capitalize)
    a     #=> ["A", "B", "C"]
#Map
* _map { | item | block } => array_
* _map => Enumerator_  

The method `map` is also used in an array.

    a = ['a', 'b', 'c']
    a.map { |x| x + x }     #=> ["aa", "bb", "cc"]
There is also a shorthand way:

    ['a', 'b', 'c'].map(&:capitalize)     #=> ["A", "B", "C"]

The element will be replaced with the value returned by block if adding a `!`

    a = ['A', 'B', 'C']
    a.map!(&:downcase)
    a     #=> ["a", "b", "c"]
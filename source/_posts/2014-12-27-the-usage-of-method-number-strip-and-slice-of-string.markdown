---
layout: post
title: "The Usage of strip, slice and split Methods of String"
date: 2014-12-27 19:49:30 +0800
comments: true
category: Ruby
author: kmc653 
---
#Strip
The method `strip` is used in a string. Returning the same content of string with leading and trailing whitespace removed. 

    "   Kevin   ".strip     #=> "Kevin"

The string will be changed if we use `strip!`. Return `nil` means the string was not altered.

    "Kevin".strip!     #=> nil

#Slice
The method `slice` is used in a string.  
* _slice( index ) => str or nil_  
* _slice( start, length ) => str or nil_  
* _slice( range ) => str or nil_  
* _slice( regexp ) => str or nil_  
* _slice( regexp, capture ) => str or nil_  
* _slice( match_str ) => str or nil_  

    "kevin".slice(3)     #=> "i"  
    "kevin".slice(3, 2)     #=> "in"  
    "kevin".slice(1..3)     #=> "evi"  
    "kevinaeiou".slice(/[aeiou][0...9]/)
    "kevin".slice("v")     #=> "v"

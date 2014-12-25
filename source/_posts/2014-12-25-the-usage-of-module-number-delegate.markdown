---
layout: post
title: "The Usage of Module#delegate"
date: 2014-12-25 11:06:29 +0800
comments: true
category: Ruby on Rails
author: kmc653
---
#Delegate

`delegate` is particularly useful with Active Record associations:  
```
class Movie < ActiveRecord::Base
  belongs_to :director
end

class Director < ActiveRecord::Base
  # has a string attribute 'name'
end
```
If we want to get the director's name of the movie, we should use `@movie.director.name`. However, if we use `delegate` in the code:
```
class Movie < ActiveRecord::Base
  belongs_to :director
  delegate :name, to: :director
end
```
We can only use `@movie.name` to get the name of director. Moreover, if we add options `prefix: true`:
```
class Movie < ActiveRecord::Base
  belongs_to :director
  delegate :name, to: :director, prefix: true
end
```
Only use `@movie.director_name` to get the director's name. It's more readable than before. We can also specify a custom prefix `:filmmaker`instead of `true`, then use `@movie.filmmaker_name` to get the same result.
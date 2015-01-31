---
layout: post
title: "Use Concerns to DRY up Model"
date: 2015-01-29 19:20:35 +0800
comments: true
category: Ruby on Rails
---
Different models in Rails application would have the code that have the same logic or functionality. Using `concerns` to put these same code in a module, which allow you to use in any models without repeating code.

#Set the path of module
At first, we should add a line of code in `config/application.rb` to tell our app where to load the module file.
{% codeblock lang:rb %}
config.autoload_paths << "#{Rails.root}/lib"
{% endcodeblock %}

#Put the same functional code in module
For instance, there is a invitation model which would generate token before creating a invitation.
```ruby invitation.rb
class Invitation < ActiveRecord::Base
  before_create :generate_token

  belongs_to :inviter, class_name: "User"

  validates_presence_of :recipient_name, :recipient_email, :message

  def generate_token
      self.token = SecureRandom.urlsafe_base64
  end
end
```
We're able to put `before_create` and `generate_token` method in a module named `Tokenable`.
```ruby tokenable.rb
module Tokenable
  extend ActiveSupport::Concern

  included do
    before_create :generate_token

    def generate_token
      self.token = SecureRandom.urlsafe_base64
    end
  end
end
```
Afterward, we add `include` following the name of module in invitation model.
``` ruby invitation.rb
class Invitation < ActiveRecord::Base
  include Tokenable

  belongs_to :inviter, class_name: "User"

  validates_presence_of :recipient_name, :recipient_email, :message
end
```
Therefore, there is no need to have redundant code to acquire the same functionality.
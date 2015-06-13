---
layout: post
title: "Test doubles and method stubs"
date: 2015-05-22 16:11:30 +0800
comments: true
category: ruby on rails, RSpec
---
Sometimes we use `test double` and `method stub` to replace objects or methods that aren't what we really want to test. It will help us write testing efficiently within a specific scope.

In the following example, I would like to test donations controller:
```ruby donations_controller.rb
class DonationsController < ApplicationController
  before_action :require_user
  
  def create
    @user = current_user
    token = params[:stripeToken]

    charge = StripeWrapper::Charge.create( 
      :source => token, 
      :description => "Donation from #{@user.email}."
    )
    if charge.successful?
      flash[:success] = "Thank you for your generous support!"
      redirect_to donate_path
    else
      flash[:error] = charge.error_message
      redirect_to donate_path
    end
  end
end
```
And the testing code:
```ruby donations_controller_spec.rb
require 'spec_helper'

describe DonationsController do
  describe "POST create" do
    
    before do
      set_current_user
    end

    context "with a successful charge" do
      
      before do
        charge = double('charge') #user test double to create a fake charge object.
        charge.stub(:successful?).and_return(true)  #stub successful? method to return true.
        StripeWrapper::Charge.stub(:create).and_return(charge)

        post :create, token: '123'
      end

      it "sets the flash success message" do
        flash[:success].should == 'Thank you for your generous support!'
      end
      it "redirects to the new dnation page" do
        response.should redirect_to donate_path
      end
    end

    context "with an failed charge" do
      
      before do
        charge = double('charge')
        charge.stub(:successful?).and_return(false)
        charge.stub(:error_message).and_return('Your card was declined')
        StripeWrapper::Charge.stub(:create).and_return(charge)
        post :create, token: '123'
      end

      it "sets the flash error message" do
        flash[:error].should == 'Your card was declined'
      end
      it "redirects to the new dnation page" do
        response.should redirect_to donate_path
      end
    end
  end
end
```

Test double object which can be think of a fake object. The inside words is just a name for the test double.

Method stub can be used to stub a specific method and return a specific value.

In above example, I've tested `StripeWrapper` before, so I should stub to replace that with something cheap which is `test double`, and also stub the result we desire. 

Hence, use `test double` and `method stub` can help us avoid some methods which computation is expensive, and make our testing specific and efficient.  
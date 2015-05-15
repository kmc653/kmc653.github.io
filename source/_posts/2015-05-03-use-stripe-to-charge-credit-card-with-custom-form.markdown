---
layout: post
title: "Use Stripe to charge credit card with custom form"
date: 2015-05-03 13:37:25 +0800
comments: true
category: ruby on rails 
---
#Why use Stripe
If you want to process credit card payment, a merchant account is needed to accept credit card. Moreover, you also need an account with Authorize.net, which is a gateway that gather credit card charge information from us, process payment, and then deposit the money into your merchant account.

However, Stripe doesn't need a merchant account and gateway. It's responsible for storing payment information, subscription and direct transfer money to your bank account. Furthermore, customer credit card will never flow through your web server by using `stripe.js`. `stripe.js` send these information to Stripe directly and get a token as response. You can use this token to process payment.

#Apply an account of Stripe
You should apply an account on [Stripe](https://stripe.com/). After applying, you will get the keys to communicate with Stripe. Moreover, you also can see the events or logs with your Stripe account.
#Installation
Add stripe in Gemfile and run `bundle install`:

    gem 'stripe'

#Payments.js file
And then, create a `payments.js` file in `app/assets/javascripts` path: 
```ruby payments.js
jQuery(function($) {
  $('#payment-form').submit(function(event) {
    var $form = $(this);
    $form.find('button').prop('disabled', true);
    Stripe.card.createToken($form, stripeResponseHandler);
    // Prevent the form from submitting with the default action
    return false;
  });
});
function stripeResponseHandler(status, response) {
  var $form = $('#payment-form');

  if (response.error) {
    // Show the errors on the form
    $form.find('.payment-errors').text(response.error.message);
    $form.find('button').prop('disabled', false);
  } else {
    // response contains id and card, which contains additional card details
    var token = response.id;
    // Insert the token into the form so it gets submitted to the server
    $form.append($('<input type="hidden" name="stripeToken" />').val(token));
    // and submit
    $form.get(0).submit();
  }
};
```
#Custome form in view template
```ruby new.html.haml
= content_for :head do
  %script(type="text/javascript" src="https://js.stripe.com/v2/")
  :javascript
    Stripe.setPublishableKey("#{ENV['STRIPE_PUBLISHABLE_KEY']}");
  = javascript_include_tag 'payments'

= render 'shared/header', header: "Donation"

%h4 Please make a donation with your credit card.
= form_tag donations_path, id: 'payment-form' do
  %fieldset.credit_card
    %span.payment-errors
    .control-group
      %label Credit Card Number
      %input.span3.card-number#card-number(type="text" data-stripe="number")
    .control-group
      %label Security Code
      %input.span3.card-cvc#security-code(type="text" data-stripe="cvc")
    .control-group
      %label Expiration
      = select_month(Date.today, {add_month_numbers: true}, name: nil, data: {stripe: "exp-month"}, class: 'card-expiry-month')
      = select_year(Date.today.year, {start_year: Date.today.year, end_year: Date.today.year + 4}, name: nil, data: {stripe: "exp-year"}, class: 'card-expiry-year')
  %fieldset.actions
    = submit_tag "Submit Payment", class: "btn btn-default"
```
Remember to add `%script(type="text/javascript" src="https://js.stripe.com/v2/")`, Stripe publish key and `javascript_include_tag 'payments'` in view template to communicate with Stripe.

#Process payment
When you fill with card information and click `Submit Payment`, you will get a token from Stripe server, and use this token to process payment.
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
In the example, it wraps payment process in a `stripe_wrapper.rb` file. The advantages of wrapping api is reusable and easy to run test.
```ruby stripe_wrapper.rb
module StripeWrapper
  class Charge
    attr_reader :response, :status

    def initialize(response, status)
      @response = response
      @status = status
    end

    def self.create(options={})
      StripeWrapper.set_api_key
      begin  
        response = Stripe::Charge.create(
          :amount => 500,
          :currency => "usd",
          :source => options[:source],
          :description => options[:description]
        )
        new(response, :success)
      rescue Stripe::CardError => e
        new(e, :error)
      end
    end

    def successful?
      status == :success
    end

    def error_message
      response.message
    end
  end

  def self.set_api_key
    Stripe.api_key = ENV['STRIPE_SECRET_KEY']
  end
end
```
#Configure API keys
In the example, I used `figaro` to configure environment variables.
```ruby application.yml
development:
  STRIPE_PUBLISHABLE_KEY: your test publishable key
  STRIPE_SECRET_KEY: your test sercet key

test:
  STRIPE_PUBLISHABLE_KEY: your test publishable key
  STRIPE_SECRET_KEY: your test sercet key

production:
  STRIPE_PUBLISHABLE_KEY: your live publishable key
  STRIPE_SECRET_KEY: your live secret key
```
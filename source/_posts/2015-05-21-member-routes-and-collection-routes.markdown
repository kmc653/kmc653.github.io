---
layout: post
title: "Member Routes and Collection Routes"
date: 2015-05-21 22:01:18 +0800
comments: true
category: ruby on rails 
---
We are able to use collection or individual members of the collection to create additional routes, rather than just seven routes of default.

#Member routes
Add a `member` block into a resource block:

    resources :conversations, only: [:index, :show, :destroy] do
      member do
        post :reply
      end
    end

After running `rake routes` in command, it will show:

                Prefix Verb   URI Pattern                               Controller#Action
    reply_conversation POST   /conversations/:id/reply(.:format)        conversations#reply
It will also create the `reply_conversation_path` and `reply_conversation_url` helpers.

You also can do like this if you only have a member route:

    resources :conversations, only: [:index, :show, :destroy] do
      post :reply, on: :member
    end

#Collection routes
Add a `collection` block into a resource block:

    resources :videos do
      collection do
        get :search
      end
    end

After running `rake routes` in command, it will show:

           Prefix Verb   URI Pattern                             Controller#Action
    search_videos GET    /videos/search(.:format)                videos#search

You also can do like this if you only have a member route:

    resources :videos do
      get :search, on: :collection
    end

Reference from [RailsGuides](http://guides.rubyonrails.org/routing.html).
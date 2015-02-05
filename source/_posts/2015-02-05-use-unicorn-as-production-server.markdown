---
layout: post
title: "Use unicorn as production server to implement concurrent requests"
date: 2015-02-05 10:33:14 +0800
comments: true
category: ruby on rails, heroku
---
It'll be much efficient use of dyno if a web application that process requests concurrently, instead of process one request at a time. Therefore, we should implement concurrent requests in developing and running production services.
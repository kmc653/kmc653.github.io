---
layout: post
title: "File Uploading on Amazon S3 with Carrierwave-aws"
date: 2015-04-30 12:10:17 +0800
comments: true
category: ruby on rails
---
Sometimes websites offer users to upload files or images. We can use Carrierwave-aws gem to upload files on Amazon S3 (Amazon simple Storage Service).

In the following example, I would like to upload large_cover and small_cover images of the videos. At first, create a migration to add a string column to the model:
```
$ rails g migration add_large_cover_and _small_cover_to_videos
$ rake db:migrate
```

```ruby 20150127011135_add_large_cover_and_small_cover_to_videos.rb
class AddLargeCoverAndSmallCoverToVideos < ActiveRecord::Migration
  def change
    add_column :videos, :large_cover, :string
    add_column :videos, :small_cover, :string
  end
end
```

And then, open the `video` model and mount the uploader:
```ruby video.rb
class Video < ActiveRecord::Base
  mount_uploader :large_cover, LargeCoverUploader
  mount_uploader :small_cover, SmallCoverUploader
end
```

Create a `uploaders` directory in `app` directory, and put `large_cover_uploader.rb` and `small_cover_uploader.rb` files in here. In these two files, we can customize our uploader, such as the path of storage or the size of image, etc.



#Resize the image
We should install `Imagemagick` and `MiniMagick` to do image resizing. MiniMagick is a Ruby interface for Imagemagick, so we should install Imagemagick first, and then install MiniMagick.

Add `carrierwave-aws` and `mini_magick`in Gemfile, and then run `bundle install`.
```ruby Gemfile
gem 'carrierwave-aws'
gem 'mini_magick'
```

Customize the image size in the `large_cover_uploader` and `small_cover_uploader` files:

```ruby large_cover_uploader.rb
class LargeCoverUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick

  process :resize_to_fill => [655, 375]
end
```

```ruby small_cover_uploader.rb
class SmallCoverUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick

  process :resize_to_fill => [166, 236]
end
```
#Configure Amazon web service
Before trying to use Amazon web service, we should apply an [AWS](http://aws.amazon.com/) account first. After having an account, we should create a new user and get `Access Key` and `Secret Access Key` of this user. And then creating a `Bucket` for the web app. There's a blog we can follow: http://blog.danielle.tuckerlabs.com/post/60491757671/creating-a-simple-aws-s3-bucket-with-iam-access#_=_ .

After creating a bucket, we should add a file called `carrierwave.rb`  in `/config/initializers`:
```ruby carrierwave.rb
CarrierWave.configure do |config|
  if Rails.env.staging? || Rails.env.production?
    config.storage    = :aws
    config.aws_acl    = :public_read
    config.aws_credentials = {
      access_key_id:      ENV.fetch('AWS_ACCESS_KEY_ID'),
      secret_access_key:  ENV.fetch('AWS_SECRET_ACCESS_KEY')
    }
    config.aws_bucket = ENV.fetch('S3_BUCKET_NAME')
  else
    config.storage = :file
    config.enable_processing = Rails.env.development?
  end
end
end
```

I use [Figaro](https://github.com/laserlemon/figaro) to configure environment variables which are set in the `application.yml` file.

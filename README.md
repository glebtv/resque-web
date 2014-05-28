resque_web
==========
[![Gem Version](http://img.shields.io/gem/v/glebtv_resque_web.svg)](https://rubygems.org/gems/glebtv_resque_web)
[![Build Status](https://img.shields.io/travis/glebtv/resque_web/master.svg)](https://travis-ci.org/glebtv/glebtv-resque-web)

A Rails-based frontend to the [Resque](https://github.com/resque/resque) job
queue system. This provides a similar interface to the existing Sinatra
application that comes bundled with Resque, but deploys like a Rails application
and leverages Rails conventions for factoring things like controllers, helpers,
and views.

# NOTICE
Note this is NOT the old sinatra interface that comes with Resque 1-x. This is
a new project based on rails. If you have any issues with old web server,
please file an issue on the [resque](https://github.com/resque/resque) project.
Note that the sinatra web interface will be gone in Resque 2.0 and this is
meant to be the replacement.

More documentation coming soon!

## Starting
Resque web is built as a rails engine.

Add it to your gemfile.

```Ruby
gem 'glebtv_resque_web'
```

Mount it in your config/routes.rb.

```Ruby
require "resque_web"

MyApp::Application.routes.draw do
  mount ResqueWeb::Engine => "/resque_web"
end
```

If you need a non-default resque server, use this environment variable.

```
RAILS_RESQUE_REDIS=123.x.0.456:6712
```
## Security

You almost certainly want to limit access when using resque-web in production. Using [routes constraints](http://guides.rubyonrails.org/routing.html#request-based-constraints) is one way to achieve this:

```ruby
# config/routes.rb

resque_web_constraint = lambda { |request| request.remote_ip == '127.0.0.1' }
constraints resque_web_constraint do
  mount ResqueWeb::Engine => "/resque_web"
end

```

Another example of a route constraint using the current user when using Devise or another warden based authentication system:

```ruby
# config/routes.rb
resque_web_constraint = lambda do |request|
  current_user = request.env['warden'].user
  current_user.present? && current_user.respond_to?(:is_admin?) && current_user.is_admin?
end

constraints resque_web_constraint do
  mount ResqueWeb::Engine => "/resque_web"
end

```

### HTTP Basic Authentication

HTTP Basic Authentication is supported out of the box. Simply set the environment variables `RESQUE_WEB_HTTP_BASIC_AUTH_USER` and `RESQUE_WEB_HTTP_BASIC_AUTH_PASSWORD` to turn it on. If you're using Resque with Heroku run `heroku config:set RESQUE_WEB_HTTP_BASIC_AUTH_USER=user RESQUE_WEB_HTTP_BASIC_AUTH_PASSWORD=secret` to get ResqueWeb secured.

## Plugins

In the past with the sinatra app it was fairly simple to just monkey-patch the
server to add more functionality/tabs. With this rails version you have to write
an engine under a specific namespace. Read more in PLUGINS.md.

## Screenshot

![Screenshot](http://i.imgur.com/LkNgl.png)

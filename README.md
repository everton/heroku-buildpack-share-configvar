Heroku buildpack: share-configvar
=========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks)
that allows one to pull in config vars from another app on dyno boot.
It is meant to be used in conjunction with other buildpacks as part of a
[multi-buildpack](https://github.com/ddollar/heroku-buildpack-multi).

The primary use of this buildpack is to allow for the sharing of Heroku Postgres
DATABASE_URL config vars between applications as recommended by [this devcenter
article](https://devcenter.heroku.com/articles/connecting-to-heroku-postgres-databases-from-outside-of-heroku).

It uses [curl](http://curl.haxx.se//) and [Heroku's v3
API](https://devcenter.heroku.com/articles/platform-api-reference).

Settings
-----

- `HEROKU_SHARE_API_EMAIL` Required
- `HEROKU_SHARE_API_TOKEN` Required
- `HEROKU_SHARE_APPNAME` Required
- `HEROKU_SHARE_CONFIGVARS` Default is DATABASE_URL

Usage
-----

Example usage:

    $ ls -a
    .buildpacks  Gemfile  Gemfile.lock  Procfile  config/  config.ru

    $ heroku config:add BUILDPACK_URL=https://github.com/ddollar/heroku-buildpack-multi.git

    $ heroku config:add HEROKU_SHARE_API_EMAIL=my-fake-email@gmail.com

    $ heroku config:add HEROKU_SHARE_API_TOKEN=`heroku auth:token`

    $ heroku config:add HEROKU_SHARE_APPNAME=ancient-everglades-9021

    $ cat .buildpacks
    https://github.com/gregburek/heroku-buildpack-share-configvar.git
    https://github.com/heroku/heroku-buildpack-ruby.git

    $ cat Procfile
    web:    bundle exec unicorn -p $PORT -c ./config/unicorn.rb -E $RACK_ENV
    worker: bundle exec rake worker

    $ git push heroku master
    ...
    -----> Fetching custom git buildpack... done
    -----> Multipack app detected
    =====> Downloading Buildpack: https://github.com/gregburek/heroku-buildpack-share-configvar.git
    =====> Detected Framework: share-configvar
    -----> Moving the share config var script into app/.profile.d
    -----> share-configvar done
    =====> Downloading Buildpack: https://github.com/heroku/heroku-buildpack-ruby.git
    =====> Detected Framework: Ruby/Rack
    -----> Using Ruby version: ruby-1.9.3
    -----> Installing dependencies using Bundler version 1.3.2
    ...

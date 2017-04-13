DEPRECATED, OBSOLET, UNMAINTAINED
=======================
Bundler has had support for specifying any build configs via envs for a while now and all of this is fully supported with heroku so there is no need to use this buildpack anymore. See this heroku article for more information:
https://devcenter.heroku.com/articles/bundler-configuration#gem-build-configuration-settings

Also apparently caching is nolonger supported the way it used to be and may be causing problems for users (as per this issue:
https://github.com/timolehto/heroku-bundle-config/issues/4). So I've simply removed those lines. I haven't been using this buildpack for anything for a long time and can't be bothered to really test this change... anyhow pls don't use this anymore. There will be no more commits to this repo.

Heroku buildpack: Heroku Bundle Config
=======================

This is a simple [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks). Having only one simple puprose. That is to seek for a folder `.heroku-bundle`. If such a file is found what this buildpack will do is it will rename the folder `.heroku-bundle` to just `.bundle`. Now, when heroku installs your gems, it will consult this folder and try to find a file `/config` in it for extra configuration options etc. to use for various gems. For more information on the structure of the file see: [This nice gist](https://gist.github.com/perplexes/5357663) (there's also a sample config file). If you do not want to manually mess with config file syntax you can refer to the [the official documentation](http://bundler.io/v1.5/man/bundle-config.1.html) and generate the config file locally via bundler commands like `bundle config --local build.some_gem "--with-cppflags='-I/app/vendor/prebuilt/include' --with-ldflags='-L/app/vendor/prebuilt/lib'"`. Obviously, this buildpack is pretty much useless on its own, so should you wish to use it you ought to use it in conjunction with the [Heroku Builpack Multi](https://github.com/ddollar/heroku-buildpack-multi).

For more ideas and a nice tutorial for doing stuff with this (and other related) tool(s), check out this Steve Took's blog article about [using charklock_holmes on Heroku](http://tooky.co.uk/using-charklock_holmes-on-heroku/).

Usage
-----

Example usage:

    #Providigin that you have somehow compiled the libraries for the Heroku dynos and copied the prebuilt libraries to vendor/prebuilt...
    $ echo >> "gem 'libarchive-ruby'" >> Gemfile
    $ bundle
    $ heroku run --app cat .bundle/config > .bundle/config #This is to copy the default stuff heroku uses on it's own as the basis...
    $ bundle config --local build.libarchive-ruby "--with-cppflags='-I/app/vendor/prebuilt/include' --with-ldflags='-L/app/vendor/prebuilt/lib'"
    $ mv .bundle .heroku-bundle #You do not want to use this version of the gem locally.. only on heroku
    $ git add .heroku-bundle Gemfile Gemfile.lock
    $ git commit -m "Added libarchive-ruby gem."
    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack
    -----> Heroku Bundle Config app detected
    -----> Found a .heroku-bundle folder

The buildpack will detect that your app has a `.heroku-bundle` folder in the root app directory. This folder will be renamed to .bundle. If a .bundle folder already exists it will be deleted.

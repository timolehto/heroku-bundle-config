Heroku buildpack: Heroku Bundle Config
=======================

This is a simple [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks). Having only one simple puprose. That is to seek for a folder `.heroku_bundle`. If such a file is found what this buildpack will do is it will rename the folder `.heroku_bundle` to just `.bundle`. Now, when heroku installs your gems, it will consult this folder and try to find a file `/config` in it for extra configuration options etc. to use for various gems. For more information on the structure of the file see: [This nice gist](https://gist.github.com/perplexes/5357663) (there's also a sample config file). If you do not want to manually mess with config file syntax you can refer to the [the official documentation](http://bundler.io/v1.5/man/bundle-config.1.html) and generate the config file locally via bundler commands like `bundle config --local build.some_gem "--with-cppflags='-I/app/vendor/prebuilt/include' --with-ldflags='-L/app/vendor/prebuilt/lib'"`. Obviously, this buildpack is pretty much useless on its own, so should you wish to use it you ought to use it in conjunction with the [Heroku Builpack Multi](https://github.com/ddollar/heroku-buildpack-multi).

Usage
-----

Example usage:

    #Providigin that you have somehow compiled the libraries for the Heroku dynos and copied the prebuilt libraries to vendor/prebuilt...
    $ echo >> "gem 'libarchive-ruby'" >> Gemfile
    $ bundle
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
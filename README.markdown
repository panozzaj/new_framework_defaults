# new_framework_defaults

I have been working on some Rails upgrades for projects, and sometimes the default `config.load_defaults` version in `config/application.rb` does not match the installed Rails version.

For example, the app might running Rails 7.2 but the app is configured to use `config.load_defaults 6.1` in `config/application.rb`. This could have been because the app wasn't correctly upgraded with `rails app:upgrade`. This usually doesn't harm the app, but it does mean that the app is not using the new defaults introduced in Rails 6.1. This makes it a bit harder to totally upgrade the app since it's a bit of technical debt.

The best way to get those older defaults is to downgrade the app to that Rails version and run `rails app:upgrade`. This is a pain, especially if the app is a couple of config versions behind.

To avoid this version juggling, in this repo I installed every point version of Rails from 5.0 (when these files were introduced) and ran `rails app:update` to generate the `new_framework_defaults.rb` file for each version. This should allow you to simply copy the appropriate file into your app in `config/initializers/` and then you can uncomment a config line at a time, and when everything looks good, to change `config.load_defaults` in `config/application.rb` to the appropriate version and remove the `new_framework_defaults` initializer.

References:

 - https://guides.rubyonrails.org/v6.0/upgrading_ruby_on_rails.html#configure-framework-defaults
 - https://www.bigbinary.com/blog/new-framework-defaults-in-rails-5-to-make-upgrade-easier

## Rails version installation challenges

Also note that for Rails 6.0+ there's an issue with the `concurrent-ruby` gem where you need to uninstall 1.3.5 and install 1.3.4 and then add that to the bundle. Something like:

```bash
gem install rails -v 6.1.7.10
gem uninstall concurrent-ruby -v 1.3.5
gem install concurrent-ruby -v 1.3.4
rails new . --skip-bundle # avoid adding 1.3.5
echo "gem 'concurrent-ruby', '1.3.4'" >> Gemfile
bundle install
bundle exec rails app:update
```

## Other notes

Note that in 5.0 the file was simply called `new_framework_defaults.rb` and in 5.1+ it is called `new_framework_defaults_5_1.rb`, etc. For consistency/simplicity, I just renamed the 5.0 one to `new_framework_defaults_5_0.rb`.

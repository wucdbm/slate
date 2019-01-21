# build prod 
bundle exec middleman build --clean

# dev server
bundle install --path vendor/bundle
bundle exec middleman server

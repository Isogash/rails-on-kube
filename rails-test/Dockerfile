FROM play_rails-test-app as old-app

# Use the barebones version of Ruby 2.2.3.
FROM ruby:2.2.3-slim

# Install dependencies:
# - build-essential: To ensure certain gems can be compiled
# - nodejs: Compile assets
RUN apt-get update && apt-get install -qq -y --fix-missing --no-install-recommends \
  build-essential \
  nodejs \
  libsqlite3-dev

# Set an environment variable to store where the app is installed to inside
# of the Docker image.
ENV INSTALL_PATH /app
RUN mkdir -p $INSTALL_PATH

# This sets the context of where commands will be ran in and is documented
# on Docker's website extensively.
WORKDIR $INSTALL_PATH

# Ensure gems are cached and only get updated when they change. This will
# drastically increase build times when your gems do not change.
ENV BUNDLE_PATH /gems
RUN mkdir -p $BUNDLE_PATH

COPY --from=old-app /gems /gems

RUN gem install bundler -v 1.12.5
# Copy both Gemfile and Gemfile.lock
COPY Gemfile Gemfile.lock ./
RUN bundle install

# Copy in the application code from your work station at the current directory
# over to the working directory.
COPY . .

# Provide dummy data to Rails so it can pre-compile assets.
RUN bundle exec rake RAILS_ENV=production assets:precompile

# Expose a volume so that nginx will be able to read in assets in production.
VOLUME ["$INSTALL_PATH/public"]

# The default command that gets ran will be to start the Unicorn server.
ENTRYPOINT bundle exec unicorn -c config/unicorn.rb

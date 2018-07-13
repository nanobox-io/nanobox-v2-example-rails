FROM ruby:2.3-alpine

# convenience ARGs from nbx
ARG DEV_PATH

# install necessary packages
RUN apk update && \
    apk add \
      # let's add some real shells for dev
      bash zsh \
      # build tools, for compiling
      build-base \
      # ncurses for pry
      ncurses \
      # nodejs for assets
      nodejs \
      # we pull some gems directly from github
      git openssh-client \
      # postgres dev headers for client
      postgresql-dev \
      # timezone files
      tzdata
      
# setup the app dir
RUN mkdir /app
WORKDIR /app

# bundle install
COPY Gemfile Gemfile.lock ./
RUN bundle install

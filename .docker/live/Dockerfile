FROM ruby:2.3-alpine


#  ██████╗ ██╗   ██╗███████╗██████╗ ██╗   ██╗██╗███████╗██╗    ██╗
# ██╔═══██╗██║   ██║██╔════╝██╔══██╗██║   ██║██║██╔════╝██║    ██║
# ██║   ██║██║   ██║█████╗  ██████╔╝██║   ██║██║█████╗  ██║ █╗ ██║
# ██║   ██║╚██╗ ██╔╝██╔══╝  ██╔══██╗╚██╗ ██╔╝██║██╔══╝  ██║███╗██║
# ╚██████╔╝ ╚████╔╝ ███████╗██║  ██║ ╚████╔╝ ██║███████╗╚███╔███╔╝
#  ╚═════╝   ╚═══╝  ╚══════╝╚═╝  ╚═╝  ╚═══╝  ╚═╝╚══════╝ ╚══╝╚══╝ 
# 
# Dockerfile used to build and configure the live environment
# 
# Steps:
#   1 - Meta (labels, build args, evars)
#   2 - Install permanent packages that the live env will need
#   3 - bundle install
#   4 - app source and config
#   5 - precompile assets
#   6 - set the command to run

# ███╗   ███╗███████╗████████╗ █████╗ 
# ████╗ ████║██╔════╝╚══██╔══╝██╔══██╗
# ██╔████╔██║█████╗     ██║   ███████║
# ██║╚██╔╝██║██╔══╝     ██║   ██╔══██║
# ██║ ╚═╝ ██║███████╗   ██║   ██║  ██║
# ╚═╝     ╚═╝╚══════╝   ╚═╝   ╚═╝  ╚═╝
                                    
# Tell nanobox to route to port 3000
LABEL io.nanobox.http_port="3000"

# convenience ARGs from nbx
ARG LIVE_PATH

# set the rails environment to production
ENV RAILS_ENV=production
# log rails to stdout
ENV RAILS_LOG_TO_STDOUT=true
# serve static assets through rails directly
ENV RAILS_SERVE_STATIC_FILES=true

# Set the image working directory to /app
WORKDIR /app


# ██████╗  █████╗  ██████╗██╗  ██╗ █████╗  ██████╗ ███████╗███████╗
# ██╔══██╗██╔══██╗██╔════╝██║ ██╔╝██╔══██╗██╔════╝ ██╔════╝██╔════╝
# ██████╔╝███████║██║     █████╔╝ ███████║██║  ███╗█████╗  ███████╗
# ██╔═══╝ ██╔══██║██║     ██╔═██╗ ██╔══██║██║   ██║██╔══╝  ╚════██║
# ██║     ██║  ██║╚██████╗██║  ██╗██║  ██║╚██████╔╝███████╗███████║
# ╚═╝     ╚═╝  ╚═╝ ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚══════╝
                                                                 
# Install necessary packages that we want to exist in the final image
RUN apk add --no-cache \
      # let's install bash so we can do useful things
      bash \
      # postgres dev headers for client
      postgresql-dev \
      # timezone files
      tzdata
      
      
# ██████╗ ██╗   ██╗███╗   ██╗██████╗ ██╗     ███████╗██████╗ 
# ██╔══██╗██║   ██║████╗  ██║██╔══██╗██║     ██╔════╝██╔══██╗
# ██████╔╝██║   ██║██╔██╗ ██║██║  ██║██║     █████╗  ██████╔╝
# ██╔══██╗██║   ██║██║╚██╗██║██║  ██║██║     ██╔══╝  ██╔══██╗
# ██████╔╝╚██████╔╝██║ ╚████║██████╔╝███████╗███████╗██║  ██║
# ╚═════╝  ╚═════╝ ╚═╝  ╚═══╝╚═════╝ ╚══════╝╚══════╝╚═╝  ╚═╝
  
# NOTE:
#   
# The trick here is that we ONLY want to pull in the Gemfile and Gemfile.lock
# at this point. Since docker creates and caches layers, docker will only
# re-run a bundle install on push if the Gemfile actually changes.
# 

# copy the Gemfile and Gemfile.lock
COPY Gemfile Gemfile.lock ./

# We will need to install some packages for the bundle to succeed, but we
# won't need or want those in production. We can roll back the install
# after the bundle command to remove them. So we want to run the entire 
# set of commands in a single RUN, so we don't end up with layers of cruft
RUN \
    # install bundle dependencies
    apk add --no-cache --virtual .bundle-deps \
      # build tools
      build-base && \
    # bundle install
    bundle install --without development test && \
    # remove bundle packages
    apk del .bundle-deps

    
#  █████╗ ██████╗ ██████╗ 
# ██╔══██╗██╔══██╗██╔══██╗
# ███████║██████╔╝██████╔╝
# ██╔══██║██╔═══╝ ██╔═══╝ 
# ██║  ██║██║     ██║     
# ╚═╝  ╚═╝╚═╝     ╚═╝     
                            
# Add the source code
COPY . /app

# copy the live config files on top of the app config
COPY ${LIVE_PATH}/config ./config/


#  █████╗ ███████╗███████╗███████╗████████╗███████╗
# ██╔══██╗██╔════╝██╔════╝██╔════╝╚══██╔══╝██╔════╝
# ███████║███████╗███████╗█████╗     ██║   ███████╗
# ██╔══██║╚════██║╚════██║██╔══╝     ██║   ╚════██║
# ██║  ██║███████║███████║███████╗   ██║   ███████║
# ╚═╝  ╚═╝╚══════╝╚══════╝╚══════╝   ╚═╝   ╚══════╝

# Same as above, the asset compilation needs some things that won't be
# needed after the compilation, so we run all of the commands in a single RUN.
RUN \
    # install compile dependencies
    apk add --no-cache --virtual .compile-deps \
      # nodejs for asset compilation
      nodejs && \
    # install yarn
    npm install -g yarn && \
    # precompile assets
    rails assets:precompile PRECOMPILE=true && \
    # remove compile packages
    apk del .compile-deps


# ██████╗ ██╗   ██╗███╗   ██╗
# ██╔══██╗██║   ██║████╗  ██║
# ██████╔╝██║   ██║██╔██╗ ██║
# ██╔══██╗██║   ██║██║╚██╗██║
# ██║  ██║╚██████╔╝██║ ╚████║
# ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═══╝
    
# run the app via puma
CMD ["puma", "-C", "/app/config/puma.rb"]

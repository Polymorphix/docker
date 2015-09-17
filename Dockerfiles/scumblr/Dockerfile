#########################################################################
#
# Dockerfile to build Scumblr container images
# Based on Ubuntu 14.04 and Scumblr (https://github.com/Netflix/Scumblr)
#
#########################################################################

# Set the base image to Ubuntu 14.04
FROM ubuntu:14.04

# File Author
MAINTAINER Michael Breuer

# Make sure the image is up to date
RUN sudo apt-get update
# RUN sudo apt-get -y upgrade

# Make admin user and change bash to that user 
RUN sudo useradd -G sudo -p '*' scumblradmin


# Install Prerequisites for Scumblr
RUN apt-get -y install git libxslt-dev libxml2-dev build-essential bison openssl zlib1g libxslt1.1 libssl-dev libxslt1-dev libxml2 libffi-dev libxslt-dev libpq-dev autoconf libc6-dev libreadline6-dev zlib1g-dev libtool libsqlite3-dev libcurl3 libmagickcore-dev ruby-build libmagickwand-dev imagemagick bundler

# Install Rbenv and Ruby
CMD cd ~
CMD  git clone git://github.com/sstephenson/rbenv.git .rbenv
CMD echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
CMD echo 'eval "$(rbenv init -)"' >> ~/.bashrc
RUN exec $SHELL

CMD git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
CMD echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
RUN exec $SHELL

CMD rbenv install 2.0.0-p481
CMD rbenv global 2.0.0-p481
RUN ruby -v

CMD exit

# Install Ruby on Rails
RUN gem install bundler --no-ri --no-rdoc
RUN rbenv rehash
RUN gem install rails -v 4.0.9  --no-ri --no-rdoc

# Install Application Dependencies
RUN sudo apt-get -y install redis-server
RUN gem install sidekiq --no-ri --no-rdoc
RUN rbenv rehash

# Setup the Scumblr Application
CMD su -l scumblradmin
CMD cd ~
CMD git clone https://github.com/Netflix/Scumblr.git
CMD cd ~/Scumblr
CMD bundle install
CMD rake db:create
CMD rake db:schema:load

# Create and admin user for Scumblr
CMD ../.rbenv/versions/2.0.0-p481/bin/rails c

CMD user = User.new
CMD user.email = "user@test.com"
CMD user.password = "test"
CMD user.password_confirmation = "test"
CMD user.admin = true
CMD user.save

CMD exit

# Expose Port for Scumblr
EXPOSE 3000

# Run Scumblr
RUN redis-server & ../.rbenv/shims/bundle exec sidekiq -l log/sidekiq.log & ../.rbenv/shims/bundle exec rails s &

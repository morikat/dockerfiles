FROM ubuntu:precise 

MAINTAINER morikat "https://github.com/morikat"

# Install packages for building ruby
RUN apt-get update
RUN apt-get install -y --force-yes build-essential curl git
RUN apt-get install -y --force-yes zlib1g-dev libssl-dev libreadline-dev libyaml-dev libxml2-dev libxslt-dev
RUN apt-get clean

# Install rbenv and ruby-build
RUN git clone https://github.com/sstephenson/rbenv.git /root/.rbenv
RUN git clone https://github.com/sstephenson/ruby-build.git /root/.rbenv/plugins/ruby-build
RUN ./root/.rbenv/plugins/ruby-build/install.sh
ENV PATH /root/.rbenv/bin:/bin:$PATH
RUN echo 'eval "$(rbenv init -)"' >> /etc/profile #/etc/profile.d/rbenv.sh or /etc/profile
RUN echo 'eval "$(rbenv init -)"' >> /.bashrc

# Install multiple versions of ruby
ENV CONFIGURE_OPTS --disable-install-doc
ADD ./versions.txt /root/versions.txt
RUN xargs -L 1 rbenv install < /root/versions.txt

# Install Bundler for each version of ruby
RUN echo 'gem: --no-rdoc --no-ri' >> /.gemrc
RUN bash -l -c 'for v in $(cat /root/versions.txt); do rbenv global $v; gem install bundler; done'

##install mysql
## http://cpw.hatenadiary.jp/entry/2013/08/20/220306
#RUN dpkg-divert --local --rename --add /sbin/initctl
#RUN ln -s /bin/true /sbin/initctl
#RUN apt-get install -y -o Dpkg::Options::="--force-confold" mysql-common
#RUN DEBIAN_FRONTEND=noninteractive apt-get install -q -y mysql-server

### use ccng gems
## mysql
RUN apt-get install -y -q mysql-client libmysqld-dev
## postgres
RUN apt-get install -y -q postgresql-client libpq-dev
## sqlite
RUN apt-get install -y -q libsqlite3-dev
#RUN gem install mysql2 -v '0.3.13'
#RUN eval "$(rbenv init -)"
#RUN rbenv rehash

## install zip & vim
#RUN bash -l -c 'DEBIAN_FRONTEND=noninteractive apt-get install -y -q -f vim zip'
RUN sudo apt-get install -y -q zip vim

RUN git clone  https://github.com/cloudfoundry/cloud_controller_ng.git /root/cloud_controller_ng
WORKDIR /root/cloud_controller_ng
RUN git submodule update --init --recursive
RUN bash -l -c 'eval "$(rbenv init -)"; bundle install --path vendor/bundle; bundle exec rspec -fs spec/dea/dea_client_spec.rb'
#RUN bundle exec rspec -fs spec/dea/dea_client_spec.rb
#CMD bundle exec parallel_rspec spec  -s 'integration|acceptance' -n 4 -o "-fs --order rand:$RANDOM" 2>&1 | tee result.txt


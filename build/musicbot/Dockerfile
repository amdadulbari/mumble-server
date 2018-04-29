FROM debian:buster
EXPOSE 6600

RUN \
    DEBIAN_FRONTEND=noninteractive \
    apt-get clean \
    && apt-get update \
    && apt-get install --no-install-recommends -qy \
        mpd mpc tmux bzip2 patch curl libyaml-dev git libopus-dev \
        build-essential zlib1g zlib1g-dev libssl-dev ruby \
        ruby-dev rubygems automake autoconf libtool psmisc \
        util-linux supervisor ca-certificates libogg-dev python-pip \
        libav-tools cron \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /opt/musicbot/src

RUN \
    git clone https://github.com/dafoxia/mumble-ruby.git \
    && git clone https://github.com/dafoxia/celt-ruby.git \
    && git clone https://github.com/dafoxia/opus-ruby.git \
    && git clone https://github.com/mumble-voip/celt-0.7.0.git celt \
    && git clone https://github.com/dafoxia/mumble-ruby-pluginbot.git \
    && (cd mumble-ruby-pluginbot && git checkout -b devel --track origin/devel)

RUN rm /opt/musicbot/src/mumble-ruby-pluginbot/plugins/radiostream.rb

WORKDIR /opt/musicbot/src/mumble-ruby

RUN gem build mumble-ruby.gemspec
RUN gem install mumble-ruby-*.gem
RUN gem install ruby-mpd
RUN gem install crack

WORKDIR /opt/musicbot/src/celt-ruby

RUN gem build celt-ruby.gemspec
RUN gem install celt-ruby

WORKDIR /opt/musicbot/src/celt

RUN \
    ./autogen.sh &&\
    ./configure --prefix=/opt/musicbot/src/celt &&\
    make &&\
    make install

WORKDIR /opt/musicbot/src/opus-ruby
RUN gem build opus-ruby.gemspec
RUN gem install opus-ruby
RUN pip install youtube-dl

WORKDIR /opt/musicbot

ADD conf/config.yml /opt/musicbot/config.yml
ADD conf/supervisord.conf /etc/supervisor/conf.d/
ADD conf/mpd.conf /opt/musicbot/mpd.conf
ADD conf/crontab.txt /tmp/crontab.txt

RUN chown -R daemon: /opt/musicbot
RUN chown daemon: /var/lib/mpd/music

RUN crontab /tmp/crontab.txt

ENTRYPOINT [ "supervisord" ]

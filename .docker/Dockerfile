FROM selenium/standalone-chrome:3.141.59

MAINTAINER Mike George <mike@tallduck.com>

USER root

RUN apt-get update -y \
 && apt-get install -y --no-install-recommends \
      build-essential \
      bzip2 \
      ca-certificates \
      curl \
      dpkg-dev \
      gcc \
      libbz2-1.0 \
      libdpkg-perl \
      libffi-dev \
      libssl-dev \
      libtimedate-perl \
      libyaml-dev \
      netbase \
      perl \
      perl-base \
      procps \
      zlib1g-dev \
      zlib1g

# skip installing gem documentation
RUN mkdir -p /usr/local/etc \
 && { \
   echo 'install: --no-document'; \
   echo 'update: --no-document'; \
 } >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.7
ENV RUBY_VERSION 2.7.1
ENV RUBY_DOWNLOAD_SHA256 d418483bdd0000576c1370571121a6eb24582116db0b7bb2005e90e250eae418

# some of ruby's build scripts are written in ruby
# we purge this later to make sure our final image uses what we just built
RUN set -ex \
  && buildDeps=' \
    ruby \
  ' \
  && apt-get update \
  && apt-get install -y --no-install-recommends $buildDeps \
    autoconf \
    bison \
    gcc \
    libbz2-dev \
    libgdbm-dev \
    libglib2.0-dev \
    libncurses-dev \
    libncurses5 \
    libncursesw5 \
    libpcre3-dev \
    libpcre3 \
    libpython-stdlib \
    libpython2.7-stdlib \
    libreadline-dev \
    libreadline6-dev \
    libtinfo-dev \
    libtinfo5 \
    libxml2-dev \
    libxslt-dev \
    make \
    ncurses-bin \
    python \
    python2.7 \
  && rm -rf /var/lib/apt/lists/* \
  && curl -fSL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
  && echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
  && mkdir -p /usr/src/ruby \
  && tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
  && rm ruby.tar.gz \
  && cd /usr/src/ruby \
  && { echo '#define ENABLE_PATH_CHECK 0'; echo; cat file.c; } > file.c.new && mv file.c.new file.c \
  && autoconf \
  && ./configure --disable-install-doc \
  && make -j"$(nproc)" \
  && make install \
  && apt-get purge -y $buildDeps \
  && gem update \
  && rm -r /usr/src/ruby

RUN gem install bundler

USER seluser

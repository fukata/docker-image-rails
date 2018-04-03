FROM ruby:2.5.1

ENV LANG C.UTF-8

RUN echo "deb http://ftp.uk.debian.org/debian stretch-backports main" >> /etc/apt/sources.list \
    && apt-get update -qq \
    && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    libfontconfig1 \
    libreadline-dev \
    libmagickwand-dev \
    libcurl3 libcurl3-gnutls \
    libcurl4-openssl-dev \
    libssl-dev \
    gettext \
    libpcre3 \
    libpcre3-dev \
    libxml2-dev \
    libxslt-dev \
    libgd-dev \
    libgeoip-dev \
    libmariadbclient-dev \
    ffmpeg \
    fonts-ipafont-gothic \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

ENV ENTRYKIT_VERSION 0.4.0
RUN wget https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && tar -xvzf entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && rm entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && mv entrykit /bin/entrykit \
    && chmod +x /bin/entrykit \
    && entrykit --symlink

RUN mkdir /app

WORKDIR /app

RUN bundle config build.nokogiri --use-system-libraries

ENTRYPOINT [ \
  "prehook", "ruby -v", "--", \
  "prehook", "bundle install -j3 --quiet", "--"]

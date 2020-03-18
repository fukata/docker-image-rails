FROM ruby:2.6.5-buster

ENV LANG C.UTF-8

RUN echo "deb http://ftp.uk.debian.org/debian buster-backports main" >> /etc/apt/sources.list \
    && apt update -qq \
    && apt install -y --no-install-recommends \
    sudo \
    build-essential \
    unzip \
    gdebi \
    libpq-dev \
    libfontconfig1 \
    libreadline-dev \
    libmagickwand-dev \
    libcurl4 \
    libcurl4-openssl-dev \
    libssl-dev \
    gettext \
    libpcre3 \
    libpcre3-dev \
    libxml2-dev \
    libxslt-dev \
    zlib1g-dev \
    libgd-dev \
    libgeoip-dev \
    libmariadbclient-dev \
    ffmpeg \
    fonts-ipafont-gothic \
    libnss3 \
    libmecab-dev \
    mecab \
    mecab-ipadic-utf8 \
    && git clone https://github.com/kornelski/giflossy.git \
    && cd giflossy \
    && autoreconf -i \
    && ./configure \
    && make \
    && make install \
    && cd ../ \
    && rm -fr giflossy \
    && git clone https://github.com/neologd/mecab-ipadic-neologd.git \
    && cd mecab-ipadic-neologd \
    && ./bin/install-mecab-ipadic-neologd -n -y \
    && cd ../ && rm -fr mecab-ipadic-neologd \
    && mv /etc/mecabrc /etc/mecabrc.old \
    && sed -e s@/var/lib/mecab/dic/debian@/usr/lib/x86_64-linux-gnu/mecab/dic/mecab-ipadic-neologd@g /etc/mecabrc.old > /etc/mecabrc \
    && cd ../ \
    && rm -fr mecab-ipadic-neologd \
    && wget -q https://chromedriver.storage.googleapis.com/2.42/chromedriver_linux64.zip \
    && unzip chromedriver_linux64.zip \
    && mv chromedriver /usr/bin/chromedriver \
    && chmod 755 /usr/bin/chromedriver \
    && rm chromedriver_linux64.zip \
    && wget -q https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && gdebi -n google-chrome-stable_current_amd64.deb \
    && rm google-chrome-stable_current_amd64.deb \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && apt install -y nodejs npm \
    && rm -rf /var/lib/apt/lists/* \
    && npm i -g npm \
    && npm install -g phantomjs-prebuilt --unsafe-perm \
    && gem install bundler:2.0.2 \
    && echo "Cleaning up image. see https://github.com/janeczku/docker-debian-s6/blob/master/docker/wheezy-backports/rootfs/usr/bin/apt-clean" \
    && apt autoremove -y \
    && apt clean \
    && apt autoclean \
    && rm -rf /tmp/* /var/tmp/* \
    && rm -rf /var/lib/apt/lists/* \
    && rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin

# Add Tini
ENV TINI_VERSION v0.14.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

RUN mkdir /app

WORKDIR /app

RUN bundle config build.nokogiri --use-system-libraries

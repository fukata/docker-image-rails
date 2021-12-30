FROM ruby:3.1.0-bullseye

ENV LANG C.UTF-8

RUN apt-get update -qq \
    && apt-get install -y --no-install-recommends \
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
    libmariadb-dev \
    ffmpeg \
    fonts-ipafont-gothic \
    libnss3 \
    libmecab-dev \
    mecab \
    mecab-ipadic-utf8

RUN git clone https://github.com/kornelski/giflossy.git \
    && cd giflossy \
    && autoreconf -i \
    && ./configure \
    && make \
    && make install \
    && cd ../ \
    && rm -fr giflossy

RUN git clone https://github.com/neologd/mecab-ipadic-neologd.git \
    && cd mecab-ipadic-neologd \
    && ./bin/install-mecab-ipadic-neologd -n -y \
    && cd ../ && rm -fr mecab-ipadic-neologd \
    && mv /etc/mecabrc /etc/mecabrc.old \
    && sed -e s@/var/lib/mecab/dic/debian@/usr/lib/x86_64-linux-gnu/mecab/dic/mecab-ipadic-neologd@g /etc/mecabrc.old > /etc/mecabrc \
    && cd ../ \
    && rm -fr mecab-ipadic-neologd

RUN wget -q https://chromedriver.storage.googleapis.com/2.42/chromedriver_linux64.zip \
    && unzip chromedriver_linux64.zip \
    && mv chromedriver /usr/bin/chromedriver \
    && chmod 755 /usr/bin/chromedriver \
    && rm chromedriver_linux64.zip \
    && wget -q https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && gdebi -n google-chrome-stable_current_amd64.deb \
    && rm google-chrome-stable_current_amd64.deb

RUN curl -sL https://deb.nodesource.com/setup_lts.x | bash - \
    && apt-get install -y nodejs \
    && npm i -g npm \
    && echo "Install Yarn" \
    && curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | tee /usr/share/keyrings/yarnkey.gpg >/dev/null \
    && echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update && apt-get install yarn

RUN echo "Cleaning up image. see https://github.com/janeczku/docker-debian-s6/blob/master/docker/wheezy-backports/rootfs/usr/bin/apt-clean" \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get autoremove -y \
    && apt-get clean \
    && apt-get autoclean \
    && rm -rf /tmp/* /var/tmp/* \
    && rm -rf /var/lib/apt/lists/* \
    && rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin

# libffi.so.6: cannot open shared object file: No such file or directory
RUN ln -s /usr/lib/x86_64-linux-gnu/libffi.so.7.1.0 /usr/lib/x86_64-linux-gnu/libffi.so.6

ENV ENTRYKIT_VERSION 0.4.0
RUN wget -q https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && tar -xvzf entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && rm entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
    && mv entrykit /bin/entrykit \
    && chmod +x /bin/entrykit \
    && entrykit --symlink

RUN mkdir /app

WORKDIR /app

RUN bundle config build.nokogiri --use-system-libraries

ENTRYPOINT [ \
  "prehook", "ruby -v", "--"]

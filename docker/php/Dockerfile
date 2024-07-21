FROM php:7.1-fpm

ARG PHP_TIMEZONE
ARG PHP_MEMORY_LIMIT
ARG USER_ID
ARG ssh_prv_key
ARG ssh_pub_key

# Configure Node.js version
#RUN curl -sL https://deb.nodesource.com/setup_8.x | bash

RUN apt-get update; \
    apt-get install -y curl gnupg; \
    curl -sL https://deb.nodesource.com/setup_8.x | bash -; \
    apt-get install -y nodejs npm; \
    rm -rf /var/lib/apt/lists/*


RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libzip-dev \
    libmagickwand-dev \
    inkscape

# Install dependencies
RUN apt-get update \
  && apt-get upgrade -y \
  && apt-get install -y --no-install-recommends \
  apt-utils \
  cron \
  git \
  mariadb-client \
  nano \
  python3 \
  python3-pip \
  redis-tools \
  rsyslog \
  sendmail \
  sendmail-bin \
  sudo \
  unzip \
  vim \
  libjpeg62-turbo-dev \
  libpng-dev \
  libfreetype6-dev \
  wget \
  libgmp-dev \
  libmagickwand-dev \
  libmagickcore-dev \
  libc-client-dev \
  libkrb5-dev \
  libicu-dev \
  libldap2-dev \
  libpspell-dev \
  librecode0 \
  librecode-dev \
  libxslt1-dev \
  libyaml-dev \
  libzip-dev \
  zip \
  && rm -rf /var/lib/apt/lists/*

# Configure the gd library
#RUN docker-php-ext-configure \
#  gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-configure \
  gd --with-freetype --with-jpeg
RUN docker-php-ext-configure \
  imap --with-kerberos --with-imap-ssl
#RUN docker-php-ext-configure \
#  ldap --with-libdir=lib/x86_64-linux-gnu
RUN docker-php-ext-configure \
  opcache --enable-opcache
#RUN docker-php-ext-configure \
#  zip --with-libzip


# Install required PHP extensions
RUN docker-php-ext-install -j$(nproc) \
  bcmath \
  exif \
  gd \
  gettext \
  gmp \
  imap \
  intl \
  ldap \
  mysqli \
  opcache \
  pdo_mysql \
  soap \
  sockets \
  sysvmsg \
  sysvsem \
  sysvshm \
  xmlrpc \
  xsl \
  zip

RUN apt-get install -y git git-core

RUN apt-get update && apt-get install -y libmagickwand-dev --no-install-recommends && rm -rf /var/lib/apt/lists/*
RUN printf "\n" | pecl install imagick
RUN docker-php-ext-enable imagick

RUN mkdir -p /tmp/libsodium  \
  && curl -sL https://github.com/jedisct1/libsodium/archive/1.0.18-RELEASE.tar.gz | tar xzf - -C  /tmp/libsodium \
  && cd /tmp/libsodium/libsodium-1.0.18-RELEASE/ \
  && ./configure \
  && make && make check \
  && make install  \
  && cd / \
  && rm -rf /tmp/libsodium  \
  && pecl install -o -f libsodium
RUN cd /tmp \
  && curl -O https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz \
  && tar zxvf ioncube_loaders_lin_x86-64.tar.gz \
  && export PHP_VERSION=$(php -r "echo PHP_MAJOR_VERSION.'.'.PHP_MINOR_VERSION;") \
  && export PHP_EXT_DIR=$(php-config --extension-dir) \
  && cp "./ioncube/ioncube_loader_lin_${PHP_VERSION}.so" "${PHP_EXT_DIR}/ioncube.so" \
  && rm -rf ./ioncube \
  && rm ioncube_loaders_lin_x86-64.tar.gz

RUN docker-php-ext-enable \
  bcmath \
  exif \
  gd \
  gettext \
  gmp \
  imap \
  intl \
  ldap \
  mysqli \
  opcache \
  pdo_mysql \
  soap \
  sockets \
  sodium \
  sysvmsg \
  sysvsem \
  sysvshm \
  xmlrpc \
  xsl \
  zip

# https://medium.com/@jack.yin/install-jpegoptim-in-ubuntu-or-centos-compress-your-images-losslessly-to-optimize-google-pagespeed-4690faa17e4c
#RUN wget http://www.kokkonen.net/tjko/src/jpegoptim-1.5.4.tar.gz
#RUN tar -xzf jpegoptim-1.5.4.tar.gz
#RUN cd jpegoptim-1.5.4
#RUN ./configure make make install
RUN apt-get update && apt-get install -y jpegoptim --no-install-recommends && rm -r /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y optipng --no-install-recommends && rm -r /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y gifsicle --no-install-recommends && rm -r /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y webp --no-install-recommends && rm -r /var/lib/apt/lists/*


#RUN apt-get install jpegoptim
#RUN apt-get install optipng
#RUN apt-get install gifsicle
#RUN apt-get install webp

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${PHP_TIMEZONE} /etc/localtime && echo ${PHP_TIMEZONE} > /etc/timezone
    
# Set timezone
RUN ln -snf /usr/share/zoneinfo/${PHP_TIMEZONE} /etc/localtime && echo ${PHP_TIMEZONE} > /etc/timezone

COPY crontab /etc/cron.d/cool-task
RUN crontab -u www-data /etc/cron.d/cool-task
RUN chmod 0644 /etc/cron.d/cool-task

RUN crontab /etc/cron.d/cool-task
#CMD cron

RUN apt-get update -yqq \
 && apt-get install -yqq \
    # install sshd
    openssh-server \
    # install ping and netcat (for debugging xdebug connectivity)
    iputils-ping netcat \
    # fix ssh start up bug
    # @see https://github.com/ansible/ansible-container/issues/141
 && mkdir /var/run/sshd \
;

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y git

RUN chown www-data:www-data /etc/cron.d/cool-task
#CMD /usr/sbin/cron -f


# Set timezone
RUN ln -snf /usr/share/zoneinfo/${PHP_TIMEZONE} /etc/localtime && echo ${PHP_TIMEZONE} > /etc/timezone

# Copy xdebug and php config.
COPY conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY conf.d/php.ini /usr/local/etc/php/conf.d/php.ini
COPY conf.d/www.conf /usr/local/etc/php-fpm.d/www.conf

# Map user id from host user when it's provided
RUN if [ ! -z ${USER_ID} ]; then usermod -u ${USER_ID} www-data; fi
RUN if [ ! -z ${USER_ID} ]; then groupmod -g ${USER_ID} www-data; fi


RUN sed -i 's#__PHP_TIMEZONE__#'"${PHP_TIMEZONE}"'#g' /usr/local/etc/php/conf.d/php.ini

COPY --from=composer:1.10.26 /usr/bin/composer /usr/local/bin/composer
#COPY --from=composer:2.3.9 /usr/bin/composer /usr/local/bin/composer

#RUN npm install -g grunt-cli

#RUN npm update


RUN mkdir /var/www/session
RUN mkdir /var/www/.composer
RUN mkdir /var/www/.composer/cache
RUN mkdir /var/www/.ssh
RUN mkdir /var/www/.npm




RUN mkdir /var/www/.magento-cloud
RUN mkdir /var/www/.magento-cloud/bin
RUN mkdir /var/www/.magento-cloud/cache
RUN mkdir /var/www/.magento-cloud/.session
RUN mkdir /var/www/.magento-cloud/.session/sess-cli-default

RUN chown www-data:www-data -R /var/www/.magento-cloud
RUN chown www-data:www-data -R /var/www/.ssh
RUN chown www-data:www-data -R /var/www/.npm
RUN chown www-data:www-data -R /usr
RUN chown www-data:www-data -R /var/www
#RUN chown www-data:www-data -R /var/www/.magento-cloud/bin
#RUN chown www-data:www-data -R /var/www/.magento-cloud/cache
#RUN chown www-data:www-data -R /var/www/.magento-cloud/.session
#RUN chown www-data:www-data -R /var/www/.magento-cloud/.session/sess-cli-default

RUN chown www-data:www-data -R /var/www/session
RUN chown www-data:www-data -R /var/www/.composer

# set default user and working directory
USER www-data
WORKDIR /var/www/project

FROM php:7.2-fpm

# Preparing for extensions install
RUN apt-get update

# Git because we have dependencies from git repos in our composer.json
RUN apt-get install -y git

# ZIP (required for Composer and Memcache install)
RUN apt-get install -y zlib1g-dev \
    && docker-php-ext-install -j$(nproc) zip

# Memcache with dependency zlib
RUN mkdir /tmp/php-pecl-memcache \
    && curl -sSL -o /tmp/php-pecl-memcache/sources.zip https://github.com/websupport-sk/pecl-memcache/archive/NON_BLOCKING_IO_php7.zip \
    && php -r "\$zip=new ZipArchive();\$zip->open('/tmp/php-pecl-memcache/sources.zip');\$zip->extractTo('/tmp/php-pecl-memcache/');" \
    && cd /tmp/php-pecl-memcache/pecl-memcache-NON_BLOCKING_IO_php7/ \
    && phpize \
    && ./configure --enable-memcache --with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && docker-php-ext-enable memcache \
    && rm -rf /tmp/php-pecl-memcache

# Pgsql driver
RUN apt-get install -y libpq-dev \
	&& docker-php-ext-install -j$(nproc) pdo_pgsql

# GD with freetype, jpeg and png dependecies
RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd

# XDebug
RUN pecl install xdebug \
	&& docker-php-ext-enable xdebug \
	&& echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# BCMath
RUN docker-php-ext-install -j$(nproc) bcmath

# PCNTL - for process control (fork, wait, etc.)
RUN docker-php-ext-install -j$(nproc) pcntl

# SOAP
RUN apt-get install -y libxml2-dev \
	&& docker-php-ext-install -j$(nproc) soap

# 7-Zip CLI for gemorroj/archive7z (required for kladr update)
RUN apt-get install -y p7zip-full

# PHP Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& php -r "unlink('composer-setup.php');"

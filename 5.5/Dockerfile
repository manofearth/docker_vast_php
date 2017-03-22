FROM php:5.5-fpm

# Preparing for extensions install
RUN apt-get update

# Memcache with dependency zlib
RUN apt-get install -y zlib1g-dev \
	&& pecl install memcache \
	&& docker-php-ext-enable memcache

# Pgsql driver
RUN apt-get install -y libpq-dev \
	&& docker-php-ext-install -j$(nproc) pdo_pgsql

# GD with freetype, jpeg and png dependecies
RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng12-dev \
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

# ZIP (required for Composer)
RUN docker-php-ext-install -j$(nproc) zip

# dBase for dbf open (required for kladr update)
RUN pecl install dbase \
	&& docker-php-ext-enable dbase

# 7-Zip CLI for gemorroj/archive7z (required for kladr update)
RUN apt-get install -y p7zip-full

# PHP Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& php -r "unlink('composer-setup.php');"

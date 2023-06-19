FROM php:8.0-fpm

# Install basic tools
RUN apt-get update && apt-get install -y \
    software-properties-common \
    curl \
    make \
    supervisor \
    unzip

# Install NGINX and required PHP extensions
RUN apt-get update && apt-get install -y \
    nginx \
    php8.0 \
    php8.0-apcu \
    php8.0-calendar \
    php8.0-common \
    php8.0-cli \
    php8.0-common \
    php8.0-ctype \
    php8.0-curl \
    php8.0-dom \
    php8.0-exif \
    php8.0-fpm \
    php8.0-gd \
    php8.0-intl \
    php8.0-mbstring \
    php8.0-mysql \
    php8.0-opcache \
    php8.0-pdo \
    php8.0-pgsql \
    php8.0-sqlite \
    php8.0-xml \
    php8.0-xsl \
    php8.0-yaml \
    php8.0-zip

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename composer

# Cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* /usr/share/man/*

# Create directory for php-fpm socket
# Link php-fpm binary file without version
# -p Creates missing intermediate path name directories
RUN ln -s /usr/sbin/php-fpm8.0 /usr/sbin/php-fpm && mkdir -p /run/php

# Initialize config files
COPY conf/fpm.conf           /usr/local/etc/php-fpm.d/
COPY conf/php.ini            /usr/local/etc/php/

WORKDIR /app

RUN apt-get update && apt-get install curl php8.0-xdebug -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && architecture=$(uname -m) \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/$architecture/$version \
    && mkdir -p /tmp/blackfire \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire-*.so $(php -r "echo ini_get ('extension_dir');")/blackfire.so \
    && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz
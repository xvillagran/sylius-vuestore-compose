FROM php:8.0-fpm-buster

ARG APP_ID=1000

RUN mkdir -p /var/www/symfony

RUN addgroup --gid ${APP_ID} --system app
RUN adduser --gid ${APP_ID} --system --shell /bin/sh -u ${APP_ID} app

RUN sed -i "s/user = www-data/user = app/g" /usr/local/etc/php-fpm.d/www.conf
RUN sed -i "s/group = www-data/group = app/g" /usr/local/etc/php-fpm.d/www.conf
RUN echo "php_admin_flag[log_errors] = on" >> /usr/local/etc/php-fpm.d/www.conf

RUN apt-get update && apt-get install -y \
  cron \
  default-mysql-client \
  git \
  gzip \
  libbz2-dev \
  libfreetype6-dev \
  libicu-dev \
  libjpeg62-turbo-dev \
  libmcrypt-dev \
  libonig-dev \
  libpng-dev \
  libsodium-dev \
  libxslt1-dev \
  libzip-dev \
  lsof \
  procps \
  vim \
  zip

RUN docker-php-ext-configure gd --with-freetype --with-jpeg

RUN docker-php-ext-install \
  bcmath \
  bz2 \
  calendar \
  exif \
  gd \
  gettext \
  intl \
  mbstring \
  mysqli \
  opcache \
  pcntl \
  pdo_mysql \
  soap \
  sockets \
  sodium \
  sysvmsg \
  sysvsem \
  sysvshm \
  xsl \
  zip

RUN pecl channel-update pecl.php.net \
  && pecl install xdebug

RUN docker-php-ext-enable xdebug \
  && sed -i -e 's/^zend_extension/\;zend_extension/g' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN apt-get install -y gnupg \
  && curl -sL https://deb.nodesource.com/setup_14.x | bash - \
  && apt-get install -y nodejs \
  && mkdir /var/www/.config /var/www/.npm \
  && chown app:app /var/www/.config /var/www/.npm \
  && npm install -g grunt-cli

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename composer

# Initialize config files
COPY conf/fpm.conf           /usr/local/etc/php-fpm.d/
COPY conf/php.ini            /usr/local/etc/php/

WORKDIR /var/www/symfony

USER app:app

# RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
#     && architecture=$(uname -m) \
#     && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/$architecture/$version \
#     && mkdir -p /tmp/blackfire \
#     && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
#     && mv /tmp/blackfire/blackfire-*.so $(php -r "echo ini_get ('extension_dir');")/blackfire.so \
#     && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz
FROM php:7.4-fpm

ENV APP_ROOT /app/web

# Install dependencies
RUN apt-get update && apt-get install -y \
        git zip unzip libpq-dev libzip-dev libpng-dev libfreetype6-dev libjpeg62-turbo-dev \
    && docker-php-ext-install -j$(nproc) bcmath pdo_mysql pdo_pgsql gd zip exif \
    && apt-get clean && apt-get autoclean

# 1.0.5 增加 GD 扩展. 图像处理
RUN apt-get update && \
apt-get install -y --no-install-recommends libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
rm -r /var/lib/apt/lists/* && \
docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
docker-php-ext-install -j$(nproc) gd

# 1.0.8 增加 zip 扩展
RUN apt-get update && \
apt-get install -y --no-install-recommends libzip-dev && \
rm -r /var/lib/apt/lists/* && \
docker-php-ext-install -j$(nproc) zip

RUN apt-get update && \
apt-get install -y --no-install-recommends libmcrypt-dev && \
rm -r /var/lib/apt/lists/* && \
pecl install mcrypt-1.0.1 && \
docker-php-ext-enable mcrypt

# 1.0.22 redis 扩展
RUN pecl install redis-4.0.1 && docker-php-ext-enable redis

ENV COMPOSER_VERSION 2.0.7

# Install composer
RUN curl -sS https://getcomposer.org/download/$COMPOSER_VERSION/composer.phar -o /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer
# config china mirror
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

# install rdkafka
RUN curl -fsSLO --compressed "https://github.com/edenhill/librdkafka/archive/v0.11.6.tar.gz" \
  && tar -xzf v0.11.6.tar.gz -C /tmp/ \
  && rm -rf v0.11.6.tar.gz \
  && cd /tmp/librdkafka-0.11.6/ \
  && ./configure \
  && make \
  && make install \
  && rm -rf /tmp/librdkafka-0.11.6 \
  && pecl install rdkafka

# add timezone
RUN echo 'date.timezone = Asia/Shanghai' >> /usr/local/etc/php/conf.d/docker-php-data-time.ini

VOLUME $APP_ROOT
WORKDIR $APP_ROOT

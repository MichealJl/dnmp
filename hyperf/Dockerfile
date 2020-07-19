FROM php:7.4.8

RUN  sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && apt-get clean

RUN apt-get update -y && \
    apt-get install -y apt-utils openssl libssl-dev && \
    pecl channel-update pecl.php.net && \
    apt-get install -y git && \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
    
USER root

    # Install the zip mysqli pdo_mysql extension
RUN apt-get install libzip-dev zip unzip -y && \
    docker-php-ext-configure zip && \
    docker-php-ext-install zip mysqli pdo_mysql

    # Install Php Redis Extension
RUN curl 'http://pecl.php.net/get/redis-5.1.1.tgz' -o /tmp/redis-5.1.1.tgz \
    && cd /tmp \
    && pecl install redis-5.1.1.tgz \
    && rm -rf /tmp/redis-5.1.1.tgz \
    && docker-php-ext-enable redis

# Install Php Swoole Extension
#ENV PHPIZE_DEPS="autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev pcre2-dev zlib-dev libtool automake"

RUN cd /tmp \
    && curl -SL "https://github.com/swoole/swoole-src/archive/v4.5.2.tar.gz" -o swoole.tar.gz \
    && ls -alh \
    # php extension:swoole
    && cd /tmp \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-mysqlnd --enable-openssl --enable-http2 \
    && make -s -j$(nproc) && make install \
    ) \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/50_swoole.ini \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/50_swoole.ini \
    # clear
    && php -v \
    && php -m \
    && php --ri swoole

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

ENTRYPOINT ["php", "/www/hyperf-api/bin/hyperf.php", "start"]

EXPOSE 9501

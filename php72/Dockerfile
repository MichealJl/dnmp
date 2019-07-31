FROM php:7.2-fpm

RUN apt-get update -yqq && \
    apt-get install -y apt-utils openssl libssl-dev && \
    pecl channel-update pecl.php.net && \
    apt-get install -y git && \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
    
USER root

    # Install the zip mysqli pdo_mysql extension
RUN apt-get install libzip-dev zip unzip -y && \
    docker-php-ext-configure zip --with-libzip && \
    docker-php-ext-install zip mysqli pdo_mysql


   # install gd iconv extension
RUN apt-get update && apt-get install -y \
      libfreetype6-dev \
      libjpeg62-turbo-dev \
      libpng-dev \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

ARG INSTALL_PHPREDIS=${INSTALL_PHPREDIS}

RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    printf "\n" | pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis \
;fi

ARG INSTALL_SWOOLE=${INSTALL_SWOOLE}

RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    # Install Php Swoole Extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl install swoole-2.0.11; \
    else \
      if [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ]; then \
        pecl install swoole-2.2.0; \
      else \
        pecl install swoole; \
      fi \
    fi && \
    docker-php-ext-enable swoole \
;fi

USER root

ARG INSTALL_IMAGEMAGICK=${INSTALL_IMAGEMAGICK}

RUN if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
    apt-get install -y libmagickwand-dev imagemagick && \
    pecl install imagick && \
    docker-php-ext-enable imagick \
;fi

#install mongo ext
ARG INSTALL_MONGO=${INSTALL_MONGO}

RUN if [ ${INSTALL_MONGO} = true ]; then \
    pecl install mongodb && \
    docker-php-ext-enable mongodb \
;fi
#install tideways_xhprof and xhgui
ARG INSTALL_TIDEWAYS_XHPROF=${INSTALL_TIDEWAYS_XHPROF}

RUN if [ ${INSTALL_TIDEWAYS_XHPROF} = true ]; then \
    git clone https://github.com/tideways/php-xhprof-extension.git && \
    cd php-xhprof-extension && \
    phpize && ./configure && \
    make && make install \
;fi
    
#amqp install
ARG INSTALL_AMQP=${INSTALL_AMQP}

RUN if [ ${INSTALL_AMQP} = true ]; then \
    # download and install manually, to make sure it's compatible with ampq installed by pecl later
    # install cmake first
    apt-get update && apt-get -y install cmake && \
    curl -L -o /tmp/rabbitmq-c.tar.gz https://github.com/alanxz/rabbitmq-c/archive/master.tar.gz && \
    mkdir -p rabbitmq-c && \
    tar -C rabbitmq-c -zxvf /tmp/rabbitmq-c.tar.gz --strip 1 && \
    cd rabbitmq-c/ && \
    mkdir _build && cd _build/ && \
    cmake .. && \
    cmake --build . --target install && \
    # Install the amqp extension
    pecl install amqp && \
    docker-php-ext-enable amqp && \
    # Install the sockets extension
    docker-php-ext-install sockets && \
    # Install bcmath
    docker-php-ext-install bcmath \
;fi	

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

CMD ["php-fpm"]

EXPOSE 9000

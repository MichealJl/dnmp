FROM php:7.2-fpm

RUN apt-get update -yqq && \
    apt-get install -y apt-utils openssl libssl-dev && \
    pecl channel-update pecl.php.net
    
USER root

RUN apt-get install libzip-dev zip unzip -y && \
    apt-get install -y libfreetype6-dev libmcrypt-dev libjpeg-dev libpng-dev && \
    docker-php-ext-configure zip --with-libzip && \
    # Install GD
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/freetype2 --with-png-dir=/usr/include --with-jpeg-dir=/usr/include && \
    # Install the zip extension
    docker-php-ext-install zip mysqli pdo_mysql gd

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

#amqp install
ARG INSTALL_AMQP=false

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

FROM    php:7.2-fpm-alpine

ENV     XDEBUG_VERSION=${XDEBUG_VERSION:-2.7.2}
ENV     PHPREDIS_VERSION=${PHPREDIS_VERSION:-4.3.0}
ENV     AMQP_VERSION=${AMQP_VERSION:-1.9.4}
ENV     SSH2_VERSION=${SSH2_VERSION:-1.1.2}

LABEL   maintainer="Mage2click"

RUN     apk add --update --no-cache \
            icu-dev \
            freetype-dev \
            libzip-dev \
            libpng-dev \
            libxml2-dev \
            libxslt-dev \
            libssh2-dev \
            rabbitmq-c-dev \
            libjpeg-turbo-dev \
            composer \
            bash\
            lsof \
            npm

RUN     docker-php-ext-configure \
            gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN     docker-php-ext-configure \
            zip --with-libzip

RUN     docker-php-ext-install \
            bcmath \
            gd \
            intl \
            mbstring \
            opcache \
            pdo_mysql \
            soap \
            xsl \
            zip

RUN     apk add --no-cache --virtual .build-deps $PHPIZE_DEPS && \
        pecl install xdebug-${XDEBUG_VERSION} \
                     redis-${PHPREDIS_VERSION} \
                     amqp-${AMQP_VERSION} \
                     ssh2-${SSH2_VERSION} && \
        docker-php-ext-enable xdebug redis amqp ssh2 && \
        sed -i -e 's/^zend_extension/\;zend_extension/g' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN     apk del .build-deps

RUN     rm -rf /var/cache/apk/* && \
        rm -rf /tmp/*

RUN     addgroup -g 1000 -S app && \
        adduser -u 1000 -S app -G app -h /var/www -s /bin/bash

RUN     echo 'PS1="\w $ "' > /var/www/.bashrc && \
        mkdir /var/www/.config /var/www/.npm && \
        chown app:app /var/www/.bashrc /var/www/.config /var/www/.npm && \
        ln -s /var/www/html/node_modules/grunt/bin/grunt /usr/bin/grunt

COPY    conf/www.conf /usr/local/etc/php-fpm.d/
COPY    conf/php.ini /usr/local/etc/php/
COPY    conf/php-fpm.conf /usr/local/etc/

RUN     mkdir /sock
RUN     chown -R app:app /usr/local/etc/php/conf.d /sock

USER    app:app

VOLUME  /var/www

WORKDIR /var/www/html
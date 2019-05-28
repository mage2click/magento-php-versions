FROM php:5.6-fpm

LABEL maintainer="Mage2click"

RUN apt-get update --fix-missing && apt-get install -y \
        cron \
        git \
        gzip \
        libfreetype6-dev \
        libicu-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libxslt-dev \
        lsof \
        mysql-client \
        vim \
        zip \
        msmtp \
        imagemagick \
        libssl-dev \
        libxml2-dev \
        wget\
        libcurl4-openssl-dev \
        unzip \
        ssh-client\
    && rm -rf /var/lib/apt/lists/*

RUN     docker-php-ext-configure \
            gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install \
        bcmath \
        gd \
        intl \
        mbstring \
        opcache \
        pdo_mysql \
        soap \
        xsl \
        zip \
        mcrypt \
        mysql \
        mysqli \
        ftp

# Install xdebug
RUN pecl install xdebug-2.5.5 \
    && docker-php-ext-enable xdebug

# Install php redis ext
RUN pecl install redis-4.2.0 \
    && docker-php-ext-enable redis \
    && rm -rf /tmp/pear

# Install composer
RUN wget -nv https://getcomposer.org/installer -O /tmp/composer-setup.php \
    && php /tmp/composer-setup.php --quiet --install-dir=/usr/local/bin --filename=composer \
    && rm /tmp/composer-setup.php

# Install mailhog/mhsendmail
RUN wget https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 \
            && chmod +x mhsendmail_linux_amd64 \
            && mv mhsendmail_linux_amd64 /usr/local/bin/mhsendmail

# Copy config files
COPY conf/php.ini /usr/local/etc/php/
COPY conf/php-fpm.conf /usr/local/etc/

# Enable PHP cli
RUN chmod ugo+rX -R /usr/local/etc/php

ENV PHP_EXTRA_CONFIGURE_ARGS --enable-fpm --with-fpm-user=app --with-fpm-group=app

# Add user and group
RUN groupadd -g 1000 app && \
    useradd -g 1000 -u 1000 -d /var/www -s /bin/bash app

RUN     mkdir /sock
RUN     chown -R app:app /usr/local/etc/php/conf.d /sock

USER    app:app

VOLUME  /var/www

WORKDIR /var/www/html
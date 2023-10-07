FROM php:8.1-fpm as base

ENV TZ="Europe/Moscow"

RUN apt-get update  \
    && apt-get install -y  \
        git \
        build-essential  \
        curl  \
        cron  \
        g++  \
        gifsicle  \
        jpegoptim  \
        libfreetype6-dev \
        libicu-dev \
        libjpeg62-turbo-dev  \
        libmagickwand-dev  \
        libmcrypt-dev  \
        libpng-dev  \
        libxml2-dev \
        libz-dev  \
        libzip-dev  \
        locales  \
        optipng  \
        pngquant  \
        zip  \
        zlib1g-dev \
        --no-install-recommends \
        libpq-dev \
        libreoffice \
        libreoffice-common \
        libreoffice-java-common \
    && apt-get clean && rm -rf /var/lib/apt/lists/* 

#
RUN docker-php-ext-configure intl
# RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg
RUN docker-php-ext-install  \
        calendar  \
        exif  \
        gd  \
        intl  \
        pcntl  \
        soap  \
        zip  \
        pdo_mysql  \
        pdo \
        pdo_pgsql \
        pgsql \
    && docker-php-ext-install opcache \
    && docker-php-ext-configure pcntl --enable-pcntl

RUN usermod --shell /bin/bash www-data

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer

WORKDIR /var/www

FROM base as dev

# установка пакетов необходимых только для разработки
RUN apt-get update  \
    && apt-get install -y nano \
    --no-install-recommends \
    && apt-get clean && rm -rf /var/lib/apt/lists/*


FROM base as production

# Копирование и настройка файлов
COPY ./docker/api/conf/php.ini /usr/local/etc/php/conf.d/user.ini
COPY ./docker/api/conf/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./app/api /var/www
COPY ./app/api/.env.example /var/www/.env

RUN chown -R www-data:www-data /var/www/ \
    && chmod -R o+rwx /var/www/storage

# Настройка CRON
COPY ./docker/api/cron/crontab /tmp/crontab
RUN crontab -l -u root | { cat /tmp/crontab } | crontab -u root -
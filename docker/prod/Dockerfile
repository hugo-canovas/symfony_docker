FROM  composer:2 AS vendor

WORKDIR /symfony

COPY symfony/composer.json symfony/composer.lock symfony/symfony.lock ./

RUN composer install --no-dev --no-scripts --prefer-dist --optimize-autoloader

FROM php:8.5-fpm-alpine3.24

ENV APP_ENV=prod
ENV APP_DEBUG=0

RUN apt-get update && apt-get install -y libpq-dev \
    && docker-php-ext-install pdo pdo_pgsql opcache \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/symfony

COPY --from=composer:2 /usr/bin/composer /user/bin/composer

COPY --from=vendor /symfony/vendor ./vendor

COPY symfony/ .

RUN composer dump-atoload --no-dev --optimize
RUN php bin/console cache:clear --env-prod --no-debug || true

RUN -p var/cache var/log
RUN chown -R www-data:www-data var

USER www-data

EXPOSE 9000

CMD ["php-fpm"]

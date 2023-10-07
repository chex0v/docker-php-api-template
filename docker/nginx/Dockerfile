FROM nginx:1.17-alpine as base

WORKDIR /var/www/

FROM base as dev

FROM base as production

COPY ./docker/nginx/conf/server.conf /etc/nginx/conf.d/default.conf
COPY ./app/public /var/www/public
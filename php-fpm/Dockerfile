# Argumente setzen
ARG PHP_VERSION

# Image erstellen
FROM php:${PHP_VERSION}-fpm-alpine

# PHP Module installieren
RUN docker-php-ext-install pdo_mysql

# Service starten
CMD ["php-fpm"]

# Ports freilegen
EXPOSE 9000

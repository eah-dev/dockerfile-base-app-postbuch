ARG PHP_VERSION=8.4
ARG BUILD_PLATFORM=alpine
ARG ALPINE_VERSION=3.23

FROM php:${PHP_VERSION}-fpm-${BUILD_PLATFORM}${ALPINE_VERSION}

LABEL maintainer="Carsten Hölbing <carsten.hoelbing@eah-jena.de>"
LABEL description="Template for PHP"

# default: UTC
ARG DATE_TIMEZONE="Europe/Berlin"

ENV PHP_APC_SHM_SIZE="128M" \
	PHP_MEMORY_LIMIT="128M" \
	PHP_OPCACHE_VALIDATE_TIMESTAMPS="0" \
	PHP_OPCACHE_MAX_ACCELERATED_FILES="10000" \
	PHP_OPCACHE_MEMORY_CONSUMPTION="192" \
	PHP_OPCACHE_MAX_WASTED_PERCENTAGE="10" \
	DATE_TIMEZONE=${DATE_TIMEZONE}

RUN set -ex; \
	\
	apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
        freetype-dev \
        #openldap-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libxml2-dev \
        libzip-dev \
        libpq-dev \
        linux-headers \
        icu-dev \
        yaml-dev \
        zlib-dev \
		gettext \
		;\
	#
	######
	# PHP extensions
	######
	#
	# Install PHP extensions bcmath
	docker-php-ext-install bcmath zip; \
	#
	# Install PHP extensions gd
	docker-php-ext-install gd && docker-php-ext-configure gd --with-freetype --with-jpeg; \
	#
	# Install PHP extensions ldap
	#docker-php-ext-install ldap && docker-php-ext-configure ldap; \
	#
	# Install PHP extensions intl (intl requires to be configured)
	docker-php-ext-install intl && docker-php-ext-configure intl; \
	#
	# Add mysqli support
	docker-php-ext-install pdo mysqli pdo_mysql && docker-php-ext-configure mysqli;

# Use the default production configuration
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
#RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# The default config can be customized by copying configuration files into the $PHP_INI_DIR/conf.d/ directory.
RUN	{ \
		echo 'memory_limit = ${PHP_MEMORY_LIMIT}'; \
		echo 'date.timezone = ${DATE_TIMEZONE}'; \
		echo 'display_errors = off'; \
		echo 'expose_php = off'; \
	} > $PHP_INI_DIR/conf.d/custom-php.ini;

# Install PHP extensions opcache
# see https://secure.php.net/manual/en/opcache.installation.php
RUN docker-php-ext-enable opcache
RUN { \
	echo 'opcache.enable = 1'; \
	echo 'opcache.interned_strings_buffer = 16'; \
	echo 'opcache.max_accelerated_files = ${PHP_OPCACHE_MAX_ACCELERATED_FILES}'; \
	echo 'opcache.max_wasted_percentage = ${PHP_OPCACHE_MAX_WASTED_PERCENTAGE}'; \
	echo 'opcache.memory_consumption = ${PHP_OPCACHE_MEMORY_CONSUMPTION}'; \
	echo 'opcache.revalidate_freq = 0'; \
	echo 'opcache.save_comments = 1'; \
	echo 'opcache.validate_timestamps = ${PHP_OPCACHE_VALIDATE_TIMESTAMPS}'; \
} >> $PHP_INI_DIR/conf.d/docker-php-ext-opcache.ini

RUN { \
	echo 'apc.enabled = 1'; \
	echo 'apc.enable_cli = 1'; \
	echo 'apc.shm_size = ${PHP_APC_SHM_SIZE}'; \
} >> $PHP_INI_DIR/conf.d/docker-php-ext-apcu.ini

USER www-data

VOLUME ["/var/www/html"]

# Setting the work directory.
WORKDIR /var/www/html
FROM php:fpm-alpine

# Use the default production configuration
#RUN cp "php.ini-production" "php.ini"

RUN apk add --no-cache --update \
	bzip2-dev \
	enchant2-dev \
	libpng-dev \
	gmp-dev \
	imap-dev \
	icu-dev \
	openldap-dev \
	freetds-dev \
	postgresql-dev \
	aspell-dev \
	net-snmp-dev \
	libxml2-dev \
	tidyhtml-dev  \
	libxslt-dev \
	libzip-dev

RUN docker-php-ext-install \
	bcmath \
	bz2 \
	calendar \
	dba \
	enchant \
	exif \
	ffi \
	gd \
	gettext \
	gmp \
	imap \
	intl \
	ldap \
	mysqli \
	opcache \
	pcntl \
	pdo_dblib \
	pdo_mysql \
	pdo_pgsql \
	pgsql \
	pspell \
	shmop \
	snmp \
	soap \
	sockets \
	sysvmsg \
	sysvsem \
	sysvshm \
	tidy \
	xsl \
	zend_test \
	zip	

RUN rm -rf /tmp/* /var/cache/apk/*
 
RUN curl -s https://getcomposer.org/installer | php \
	&& mv composer.phar /usr/local/bin/composer \
	&& chmod +x /usr/local/bin/composer


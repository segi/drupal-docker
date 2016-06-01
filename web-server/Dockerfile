FROM php:5.6.21-fpm-alpine

# define workdir to home
WORKDIR /install
	 
	  # install composer
RUN   \
	  curl -sS https://getcomposer.org/installer | php \
	  && mv composer.phar /usr/local/bin/composer

# install the PHP extensions we need
RUN apk update && apk add -U bash curl apache2 libpng libjpeg libpq wget mysql-client openssh \
	&& rm -rf /var/lib/apt/lists/* \
	#&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
	&& apk add php-pdo php-pdo_mysql php-pdo_pgsql zip
#RUN usermod -u 1000 www-data
#RUN a2enmod rewrite

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Install Composer.
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# Prepare for SSH
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/init.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# Install Drush 8 (master) as phar.
RUN wget http://files.drush.org/drush.phar
RUN mv drush.phar /usr/local/bin/drush && chmod +x /usr/local/bin/drush

RUN cp /usr/src/php/php.ini-development /usr/local/etc/php/php.ini

# Configure PHP.
RUN sed -i 's/display_errors = Off/display_errors = On/' /usr/local/etc/php/php.ini
RUN sed -i 's/display_errors = Off/display_errors = On/' /usr/local/etc/php/php.ini

EXPOSE 80 22

WORKDIR /var/www/html
COPY entrypoint.sh /docker-entrypoint
RUN chmod +x /docker-entrypoint
ENTRYPOINT ["/docker-entrypoint"]




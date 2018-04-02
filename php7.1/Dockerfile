FROM ubuntu:16.04

MAINTAINER Nguyen Huu Kim <kimnguyen.ict@gmail.com>

# Update dependencies ubuntu:
RUN apt-get update \
    && apt-get install -y software-properties-common locales \
    && locale-gen en_US.UTF-8

ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LC_CTYPE=UTF-8
ENV LANG=en_US.UTF-8
ENV TERM xterm

# Set apache user group www-data:
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

# Install Apache 2.4 and PHP 7.1:
RUN add-apt-repository ppa:ondrej/php \
  && apt-get update \
  && apt-get install -y \
        php7.1 \
        php7.1-mysql \
        php7.1-common \
        php7.1-mbstring \
        php7.1-xml \
        php7.1-json \
        php7.1-tokenizer \
        php7.1-curl \
        php7.1-zip \
        php7.1-gd \
        libapache2-mod-php7.1

# Fix Apache connection refused:
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Rewrite apache default config:
RUN echo "\
<VirtualHost *:80>\n\
    ServerAdmin webmaster@localhost\n\
    DocumentRoot /var/www/html\n\n\
\
    <Directory /var/www/html>\n\
        Options FollowSymLinks\n\
        AllowOverride All\n\
        Require all granted\n\
    </Directory>\n\n\
\
    ErrorLog ${APACHE_LOG_DIR}/error.log\n\
    CustomLog ${APACHE_LOG_DIR}/access.log combined\n\
</VirtualHost>\n\n\
\
# vim: syntax=apache ts=4 sw=4 sts=4 sr noet\n\
" > /etc/apache2/sites-available/000-default.conf

# Install lirary: curl, nano and ping:
RUN apt-get install -y curl nano iputils-ping

# Install Composer:
RUN curl -s http://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && echo "export PATH=${PATH}:/var/www/workspace/vendor/bin:/root/.composer/vendor/bin" >> ~/.bashrc

# Allow write permission:
RUN usermod -u 1000 www-data \
    && chown www-data -R /var/www/html \
    && a2enmod rewrite

EXPOSE 80
EXPOSE 443

WORKDIR /var/www/html

ENTRYPOINT service apache2 restart && /bin/bash

FROM php:8.1-apache

LABEL maintainer="Jefferson Vantuir <contato@jeffersonvantuir.com.br>" \
  org.label-schema.name="jeffersonvantuir/php-apache" \
  org.label-schema.description=":coffee: Docker image for PHP and Apache" \
  org.label-schema.build-date=$BUILD_DATE \
  org.label-schema.schema-version="1.0" \
  org.label-schema.vcs-url="https://github.com/jeffersonvantuir/docker-php-apache"

USER root

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && sync

RUN apt-get update && apt-get upgrade -y

RUN install-php-extensions \
    mysqli \
    pdo \
    pdo_mysql \
    intl \
    snmp \
    ctype \
    zip

RUN docker-php-ext-enable mysqli
   
### COMPOSER INSTALL ##
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

### CONFIG APACHE ###
RUN echo "" >> /etc/apache2/sites-available/000-default.conf && rm /etc/apache2/sites-available/000-default.conf
ADD ./webserver.conf /etc/apache2/sites-available/webserver.conf
RUN cd /etc/apache2/sites-enabled && ln -S webserver.conf /etc/apache2/sites-available/webserver.conf

RUN a2enmod rewrite

### ADD USER app ###
RUN userdel app &> /dev/null && adduser --disabled-password --gecos "" app 
RUN echo "app  ALL = ( ALL ) NOPASSWD: ALL" >> /etc/sudoers
RUN chown -R app:app /var/www $HOME

USER app

WORKDIR /var/www/html
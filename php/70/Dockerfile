FROM ubuntu:20.04

LABEL maintainer=tronglt

WORKDIR /var/www/application

# Avoid prompts when building
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get install -y gnupg git curl vim

# Add Ondrej's deb package to fetch PHP source.
RUN mkdir -p ~/.gnupg \
    && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E5267A6C \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C300EE8C \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu focal main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
    && apt-get update

# Install PHP and it's dependencies.
RUN apt-get install -y php7.0-fpm \
    php7.0-cli \
    php7.0-mcrypt \
    php7.0-gd \
    php7.0-curl \
    php7.0-xml \
    php7.0-zip \
    php7.0-mbstring \
    php7.0-mysql \
    php7.0-soap \
    php7.0-xdebug

# Install Composer.
RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && composer self-update --1

# Config PHP-FPM.
RUN sed -i "s/pm\.max_children = .*/pm.max_children = 20/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm\.start_servers = .*/pm.start_servers = 10/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm\.min_spare_servers = .*/pm.min_spare_servers = 5/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm\.max_spare_servers = .*/pm.max_spare_servers = 10/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/listen = .*/listen = 0.0.0.0:9000/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i 's/^;daemonize.*$/daemonize = no/g' /etc/php/7.0/fpm/php-fpm.conf \
    && sed -i 's@^error_log.*$@error_log = /proc/self/fd/2@g' /etc/php/7.0/fpm/php-fpm.conf \
    && echo "xdebug.mode=develop,debug" >> /etc/php/7.0/mods-available/xdebug.ini && \
    && echo "xdebug.start_with_requests=yes" >> /etc/php/7.0/mods-available/xdebug.ini && \
    && echo "xdebug.discover_client_host=0" >> /etc/php/7.0/mods-available/xdebug.ini && \
    && echo "xdebug.client_host=host.docker.internal" >> /etc/php/7.0/mods-available/xdebug.ini

RUN apt-get update

CMD [ "php-fpm7.0" ]


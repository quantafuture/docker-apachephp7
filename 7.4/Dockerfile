FROM debian:buster

MAINTAINER Ghislain GAUCHER <ghislain@lancaster-solutions.com>
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
# SSH
COPY ./config/ssh/config /root/.ssh/
RUN apt -y update \
 && apt -y upgrade \
# Packages
 && apt install -y --no-install-recommends \
    acl \
    apt-transport-https \
    ca-certificates \
    curl \
    git \
    ssh-client \
    gnupg \
    htop \
    libpng-dev \
    make \
    unzip \
    vim \
    wget \
    redis-tools \
    imagemagick \
    ghostscript \
# Node and NPM
 && curl -sL https://deb.nodesource.com/setup_8.x > setup_node && chmod +x setup_node && ./setup_node \
 && apt install -y npm nodejs \
 && apt -y update \
 && npm install -g npm-cli-login \
# Amqproxy
 && wget -qO- https://packagecloud.io/cloudamqp/amqproxy/gpgkey | apt-key add - \
 && echo "deb https://packagecloud.io/cloudamqp/amqproxy/ubuntu/ bionic main" | tee /etc/apt/sources.list.d/amqproxy.list \
 && apt -y update \
 && apt install -y amqproxy \
# PHP
 && wget -q https://packages.sury.org/php/apt.gpg -O- | apt-key add - \
 && echo "deb https://packages.sury.org/php/ buster main" | tee /etc/apt/sources.list.d/php.list \
 && apt -y update \
 && apt install -y --no-install-recommends \
    php7.4 \
    php7.4-bcmath \
    php7.4-curl \
    php7.4-dev \
    php7.4-gd \
    php7.4-iconv \
    php7.4-imap \
    php7.4-intl \
    php7.4-mbstring \
    php7.4-mysql \
    php7.4-redis \
    php7.4-soap \
    php7.4-xdebug \
    php7.4-xml \
    php7.4-zip \
    php7.4-imagick \
 && update-alternatives --set php /usr/bin/php7.4 \
# PECL
 && apt install -y --no-install-recommends php-pear php7.4-dev \
 && pecl install timecop-beta \
# Apache
 && apt install -y --no-install-recommends apache2 libapache2-mod-php7.4 \
 && a2dismod mpm_event php7.4 \
 && a2enmod mpm_prefork php7.4 rewrite ssl macro headers proxy proxy_http proxy_wstunnel \
 && mv /etc/apache2/apache2.conf /etc/apache2/apache2.conf.dist \
 && rm /etc/apache2/conf-enabled/* /etc/apache2/sites-enabled/* \
# PHP ini
 && echo "date.timezone=Europe/Paris" > /etc/php/7.4/cli/conf.d/timezone.ini \
 && echo "date.timezone=Europe/Paris" > /etc/php/7.4/apache2/conf.d/timezone.ini \
 && echo "memory_limit=2048M" > /etc/php/7.4/cli/conf.d/memory-limit.ini \
 && echo "memory_limit=2048M" > /etc/php/7.4/apache2/conf.d/memory-limit.ini \
 && echo "extension=timecop.so" > /etc/php/7.4/cli/conf.d/20-timecop.ini \
 && echo "extension=timecop.so" > /etc/php/7.4/apache2/conf.d/20-timecop.ini \
# Bashrc
 && echo "alias ls='ls --color=auto'" >> .bashrc \
 && echo "alias ll='ls -halF'" >> .bashrc \
#cleanup
 && apt clean -y \
 && rm -rf /var/lib/apt/lists/*
# APACHE
COPY ./config/apache2.conf /etc/apache2/apache2.conf
COPY ./apache2-foreground /usr/local/bin/

EXPOSE 80
WORKDIR /var/www
ENTRYPOINT ["apache2-foreground"]

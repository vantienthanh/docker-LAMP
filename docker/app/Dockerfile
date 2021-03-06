FROM ubuntu:latest

MAINTAINER Thanh VT

RUN apt-get update \
    && apt-get install --no-install-recommends -y software-properties-common locales supervisor \
    && apt-get update \
    && locale-gen en_US.UTF-8

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

RUN add-apt-repository -r ppa:ondrej/apache2 -y \
    && add-apt-repository ppa:ondrej/php -y \
    && apt-get update \
    && apt-get install --no-install-recommends -y \
        software-properties-common \
        apache2 \
        apache2-utils \
        php7.4-fpm \
        php7.4-cli \
        php7.4-dev \
        php7.4-xdebug \
        libapache2-mod-php7.4 \
        php7.4-pdo \
        php7.4-pdo-mysql \
        php7.4-sqlite3 \
        php7.4-xml \
        php7.4-mbstring \
        php7.4-tokenizer \
        php7.4-zip \
        php7.4-gd \
        php7.4-curl \
        php7.4-imap \
        php-imagick \
        curl \
        vim \
    && mkdir /run/php \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    # && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    # && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    # && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    # && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /etc/php/7.4/mods-available/blackfire.ini \
    # && phpenmod blackfire \
    && apt-get remove -y --purge software-properties-common curl \
    && apt-get clean \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && rm /etc/apache2/sites-enabled/000-default.conf \
    && a2enmod suexec rewrite ssl actions include cgi actions proxy_fcgi alias headers

COPY default.conf /etc/apache2/sites-available/default.conf
COPY php.ini /etc/php/7.4/fpm/php.ini
COPY php-fpm.conf /etc/php/7.4/fpm/php-fpm.conf
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY laravel.ini /etc/php/7.4/fpm/conf.d/laravel.ini
COPY enabled-xdebug.ini /etc/php/7.4/mods-available/xdebug.ini

RUN a2ensite default.conf

# RUN service apache2 restart
RUN /etc/init.d/php7.4-fpm restart

# RUN mkdir /tmp/certgen
# WORKDIR /tmp/certgen
# RUN openssl genrsa -des3 -passout pass:x -out server.pass.key 2048 \
#     && openssl rsa -passin pass:x -in server.pass.key -out server.key \
#     && rm server.pass.key \
#     && openssl req -new -key server.key -out server.csr -subj "/CN=asgardcms.com" \
#     && openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt \
#     && cp server.crt /etc/ssl/certs/ \
#     && cp server.key /etc/ssl/private/ \
#     && rm -rf /tmp/certgen

EXPOSE 80
EXPOSE 443

WORKDIR /var/www/html

CMD ["supervisord"]
FROM php:7.4-fpm

WORKDIR /usr/local/apache2/htdocs

RUN apt-get update && apt-get install -y \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libpng-dev \
  && docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install -j$(nproc) gd

RUN apt-get update && apt-get install -y \
  libzip-dev \
  zip \
  libxml2-dev \
  && docker-php-ext-install zip soap mysqli pdo_mysql

#GD
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install -j "$(nproc)" gd

RUN apt-get update && apt-get -y install wget bsdtar libaio1 && \
  wget -qO- https://raw.githubusercontent.com/caffeinalab/php-fpm-oci8/master/oracle/instantclient-basic-linux.x64-12.2.0.1.0.zip | bsdtar -xvf- -C /usr/local && \
  wget -qO- https://raw.githubusercontent.com/caffeinalab/php-fpm-oci8/master/oracle/instantclient-sdk-linux.x64-12.2.0.1.0.zip | bsdtar -xvf-  -C /usr/local && \
  wget -qO- https://raw.githubusercontent.com/caffeinalab/php-fpm-oci8/master/oracle/instantclient-sqlplus-linux.x64-12.2.0.1.0.zip | bsdtar -xvf- -C /usr/local && \
  ln -s /usr/local/instantclient_12_2 /usr/local/instantclient && \
  ln -s /usr/local/instantclient/libclntsh.so.* /usr/local/instantclient/libclntsh.so && \
  ln -s /usr/local/instantclient/lib* /usr/lib && \
  ln -s /usr/local/instantclient/sqlplus /usr/bin/sqlplus && \
  docker-php-ext-configure oci8 --with-oci8=instantclient,/usr/local/instantclient && \
  docker-php-ext-install oci8 && \
  rm -rf /var/lib/apt/lists/* && \
  php -v

RUN wget http://php.net/distributions/php-7.4.0.tar.gz && \
  mkdir php_oci && \
  mv php-7.4.0.tar.gz ./php_oci
WORKDIR /usr/local/apache2/htdocs/php_oci
RUN tar xfvz php-7.4.0.tar.gz
WORKDIR /usr/local/apache2/htdocs/php_oci/php-7.4.0/ext/pdo_oci
RUN phpize && \
  ./configure --with-pdo-oci=instantclient,/usr/local/instantclient,12.1 && \
  make && \
  make install && \
  echo extension=pdo_oci.so > /usr/local/etc/php/conf.d/pdo_oci.ini && \
  php -v

COPY php.ini /usr/local/etc/php/php.ini

#Composer
RUN curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/ \
  && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer


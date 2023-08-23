FROM php:8.1.1-fpm

ARG USER
ARG UID=1000

RUN apt-get update && apt-get install -y \
    cron \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Clean Cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd sockets intl

# Old NodeJS
RUN curl -sL https://deb.nodesource.com/setup_18.x  | bash -
RUN apt-get -y install nodejs
RUN npm install --global cross-env
RUN npm install -g npm@latest


# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run bash commands
RUN useradd -G www-data,root -u $UID -d /home/$USER $USER
RUN mkdir -p /home/$USER/.composer && \
    chown -R $USER:$USER /home/$USER

# Install Redis
RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

COPY ./docker/php.ini /usr/local/etc/php/conf.d/custom-php.ini

WORKDIR /var/www

# Permissions to workdir
RUN chown -R $UID:$UID /var/www/

USER $USER

ENV PORT=$PORT
ENTRYPOINT [ "docker/entrypoint.sh" ]

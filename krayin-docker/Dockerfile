# main image
FROM php:8.3-apache

# installing dependencies
RUN apt-get update && apt-get install -y \
    git \
    ffmpeg \
    libfreetype6-dev \
    libicu-dev \
    libgmp-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libwebp-dev \
    libxpm-dev \
    libzip-dev \
    unzip \
    zlib1g-dev

# configuring php extension
RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp
RUN docker-php-ext-configure intl

# installing php extension
RUN docker-php-ext-install bcmath calendar exif gd gmp intl mysqli pdo pdo_mysql zip

# installing composer
COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer

# installing node js via nodejs.org installer script
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs

# installing global node dependencies (npx is included with npm)
RUN npm install -g laravel-echo-server

# arguments
ARG container_project_path
ARG uid
ARG user

# setting work directory
WORKDIR $container_project_path

# adding user
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# setting apache
COPY ./.configs/apache.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

# setting up project from `src` folder
RUN chmod -R 775 $container_project_path
RUN chown -R $user:www-data $container_project_path

# changing user
USER $user

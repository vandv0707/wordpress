FROM wordpress:apache

# Enable Apache modules
RUN a2enmod rewrite headers

# Install necessary tools for running the application
RUN apt-get update && \
    apt-get install -y \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libzip-dev \
        libicu-dev \
        libpq-dev \
        vim \
        nano \
        mc \
        htop \
        autoconf && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Configure GD extension
RUN docker-php-ext-configure gd --with-freetype --with-jpeg

# Install PHP extensions
RUN docker-php-ext-install \
    bcmath \
    fileinfo \
    gd \
    intl \
    opcache \
    pgsql \
    pdo_pgsql \
    zip \
    mysqli \
    pdo \
    pdo_mysql

# Set Apache document root
ENV APACHE_DOCUMENT_ROOT=/var/www/html
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# Start with base PHP config, then add extensions
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# Composer
COPY --from=composer:2.1.11 /usr/bin/composer /usr/bin/composer

# Create a user with the same UID/GID as the host user
ARG uid
RUN useradd -G www-data,root -u $uid -d /home/www www \
    && mkdir -p /home/www/.composer \
    && chown -R www:www /home/www

# Copy application files and set ownership
COPY --chown=www:www . /var/

# Switch to the newly created user
USER www

# Expose port 80
EXPOSE 80

# Start Apache server
CMD ["apache2-foreground"]

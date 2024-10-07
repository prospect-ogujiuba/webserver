# Use the official PHP 8.3 FPM base image
FROM php:8.3-fpm

# Install system dependencies and PHP extensions commonly required
RUN apt-get update && apt-get install -y \
    vim \
    nano \
    git \
    curl \
    wget \
    zsh \
    libpng-dev \
    libjpeg-dev \
    libwebp-dev \
    libfreetype6-dev \
    libonig-dev \
    libzip-dev \
    unzip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd mbstring zip opcache pdo pdo_mysql mysqli \
    && pecl install redis xdebug \
    && docker-php-ext-enable redis xdebug opcache pdo_mysql \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Composer globally
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install Node.js and NPM (for Laravel Mix and similar tools)
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm

# Install Oh My Zsh and set Zsh as default shell
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" \
    && chsh -s $(which zsh)

# Add Composer's global bin directory to PATH
ENV PATH="/root/.composer/vendor/bin:${PATH}"

# Install Laravel Installer globally
RUN composer global require laravel/installer

# Install WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

# Set permissions for the container user
RUN chown -R www-data:www-data /var/www/html

# Set the working directory
WORKDIR /var/www/html

# Expose port 9000 and start PHP-FPM
EXPOSE 9000
CMD ["php-fpm"]

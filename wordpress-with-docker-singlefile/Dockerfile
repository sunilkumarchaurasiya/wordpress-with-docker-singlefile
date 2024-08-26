FROM ubuntu:22.04

# Set timezone to UTC to avoid configuration prompts
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Update package list and install dependencies
RUN apt-get update && \
    apt-get install -y \
    tzdata \
    nginx \
    software-properties-common \
    wget \
    unzip \
    curl \
    mysql-server \
    php8.1 \
    php8.1-cli \
    php8.1-fpm \
    php8.1-mysql \
    php8.1-pdo \
    php8.1-zip \
    php8.1-gd \
    php8.1-mbstring \
    php8.1-curl \
    php8.1-xml \
    php8.1-bcmath \
    php8.1-intl \
    php8.1-soap \
    php8.1-xsl \
    php8.1-tidy \
    php8.1-opcache \
    php8.1-readline \
    php8.1-imagick \
    php8.1-ldap \
    php8.1-xmlrpc \
    php8.1-sqlite3 \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Configure MySQL
RUN service mysql start && \
    mysql -e "CREATE USER 'sunil'@'localhost' IDENTIFIED BY 'password';" && \
    mysql -e "CREATE DATABASE sunil;" && \
    mysql -e "GRANT ALL PRIVILEGES ON sunil.* TO 'sunil'@'localhost';" && \
    mysql -e "FLUSH PRIVILEGES;"

# Download and unzip WordPress
RUN wget https://wordpress.org/latest.zip && \
    unzip latest.zip -d /var/www/html/ && \
    rm latest.zip && \
    mv /var/www/html/wordpress /var/www/html/website

# Set the working directory to WordPress directory
WORKDIR /var/www/html/website

# Adjust ownership and permissions for WordPress
RUN chown -R www-data:www-data /var/www/html/website && \
    find /var/www/html/website -type d -exec chmod 755 {} \; && \
    find /var/www/html/website -type f -exec chmod 644 {} \;

# Configure wp-config.php with database details
RUN cp wp-config-sample.php wp-config.php && \
    sed -i "s/database_name_here/sunil/" wp-config.php && \
    sed -i "s/username_here/sunil/" wp-config.php && \
    sed -i "s/password_here/password/" wp-config.php && \
    sed -i "s/localhost/127.0.0.1/" wp-config.php

# Copy custom Nginx configuration to sites-available
COPY nginx.conf /etc/nginx/sites-available/wordpress

# Remove the default Nginx configuration
RUN rm /etc/nginx/sites-enabled/default

# Enable the custom Nginx configuration
RUN ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/

# Expose port 80 for Nginx
EXPOSE 80

# Start services (MySQL, PHP-FPM, and Nginx)
CMD ["sh", "-c", "service mysql start && service php8.1-fpm start && nginx -g 'daemon off;'"]


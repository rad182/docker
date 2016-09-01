FROM richarvey/nginx-php-fpm:php5

# Install some other php mmodules
RUN apk add --no-cache php5-apcu php5-pear php5-phalcon

# Add nginx config
ADD conf/nginx-site.conf /etc/nginx/sites-available/default.conf


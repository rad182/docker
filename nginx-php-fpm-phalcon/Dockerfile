FROM richarvey/nginx-php-fpm:php5

# Install some other php mmodules
RUN apk add --no-cache php5-apcu php5-pear php5-phalcon

# Copy phalcon.ini to the correct directory
RUN cp /etc/php/conf.d/phalcon.ini /etc/php5/conf.d/phalcon.ini

# Add nginx config
ADD conf/nginx-site.conf /etc/nginx/sites-available/default.conf


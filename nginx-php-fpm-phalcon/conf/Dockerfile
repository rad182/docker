FROM richarvey/nginx-php-fpm:php5

# Install some other php mmodules
RUN apk add --no-cache php5-apcu php5-pear

# Also install php5-mongo. Since there's no official package for this yet
RUN apk --no-cache add ca-certificates
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-php5-mongo/master/sgerrand.rsa.pub
RUN wget https://github.com/sgerrand/alpine-pkg-php5-mongo/releases/download/1.16.4-r0/php5-mongo-1.6.14-r0.apk
RUN apk add php5-mongo-1.6.14-r0.apk

# Add nginx config
ADD conf/nginx-site.conf /etc/nginx/sites-available/default.conf


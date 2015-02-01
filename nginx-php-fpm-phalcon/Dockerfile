FROM ubuntu:14.04.1
MAINTAINER Royce Albert Dy <rady182@me.com>

# Surpress Upstart errors/warning
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

# Add sources for latest nginx
RUN apt-get install -y wget
RUN wget -q http://nginx.org/keys/nginx_signing.key -O- | sudo apt-key add -
RUN echo deb http://nginx.org/packages/mainline/ubuntu/ trusty nginx >> /etc/apt/sources.list
RUN echo deb-src http://nginx.org/packages/mainline/ubuntu/ trusty nginx >> /etc/apt/sources.list

# Update System
RUN apt-get update
RUN apt-get -y upgrade

# Install Basic Requirements
RUN apt-get -y install nginx python-software-properties python-setuptools software-properties-common curl

# Install PHP
RUN apt-get -y install php5-fpm php5-mysql php-apc php5-cli php5-dev

# Install Extra PHP Modules
RUN apt-get -y install php5-curl php5-gd php-pear php5-mcrypt php5-mongo

# Install Memcached
RUN apt-get -y install memcached php5-memcached php5-memcache

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# Install Phalcon
RUN apt-add-repository ppa:phalcon/stable -y
RUN apt-get update
RUN apt-get -y install php5-phalcon

# tweak nginx config
RUN sed -i -e"s/worker_processes 1/worker_processes 5/" /etc/nginx/nginx.conf # gets over written by start.sh to match cpu's on container
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN sed -i "s/.*conf\.d\/\*\.conf;.*/&\n    include \/etc\/nginx\/sites-enabled\/\*;/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# tweak php-fpm config
RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
RUN sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_children = 5/pm.max_children = 9/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.start_servers = 2/pm.start_servers = 3/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.min_spare_servers = 1/pm.min_spare_servers = 2/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_spare_servers = 3/pm.max_spare_servers = 4/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_requests = 500/pm.max_requests = 200/g" /etc/php5/fpm/pool.d/www.conf

# fix ownership of sock file for php-fpm as our version of nginx runs as nginx
RUN sed -i -e "s/user = www-data/user = nginx/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/group = www-data/group = nginx/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/listen.owner = www-data/listen.owner = nginx/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/listen.group = www-data/listen.group = nginx/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/;listen.mode = 0660/listen.mode = 0750/g" /etc/php5/fpm/pool.d/www.conf
RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;

# nginx site conf
RUN rm -Rf /etc/nginx/conf.d/*
RUN mkdir -p /etc/nginx/sites-available/
RUN mkdir -p /etc/nginx/sites-enabled/
RUN mkdir -p /etc/nginx/ssl/
ADD ./nginx-site.conf /etc/nginx/sites-available/default.conf
RUN ln -s /etc/nginx/sites-available/default.conf /etc/nginx/sites-enabled/default.conf

# add test PHP file
ADD ./index.php /usr/share/nginx/html/index.php
RUN chown -Rf nginx.nginx /usr/share/nginx/html/

# Supervisor Config
RUN /usr/bin/easy_install supervisor
RUN /usr/bin/easy_install supervisor-stdout
ADD ./supervisord.conf /etc/supervisord.conf

# Start Supervisord
ADD ./start.sh /start.sh
RUN chmod 755 /start.sh

# Expose Ports
EXPOSE 443
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]
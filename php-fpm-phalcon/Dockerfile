FROM ubuntu:14.04.1
MAINTAINER Royce Albert Dy <royce@hoyrewards.com>

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get -y upgrade

# Install Basic Requirements
RUN apt-get -y install python-software-properties software-properties-common

# Install PHP
RUN apt-get -y install php5-fpm php5-mysql php-apc php5-cli php5-dev

# Install Extra PHP Modules
RUN apt-get -y install php5-curl php5-gd php-pear php5-mcrypt php5-mongo

# Install Memcached
RUN apt-get -y install memcached php5-memcached php5-memcache

# Install Phalcon
RUN apt-add-repository ppa:phalcon/stable -y
RUN apt-get update
RUN apt-get -y install php5-phalcon

# tweak php-fpm config
RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=1/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
#RUN sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_children = 5/pm.max_children = 9/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.start_servers = 2/pm.start_servers = 3/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.min_spare_servers = 1/pm.min_spare_servers = 2/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_spare_servers = 3/pm.max_spare_servers = 4/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/pm.max_requests = 500/pm.max_requests = 200/g" /etc/php5/fpm/pool.d/www.conf

# fix ownership of sock file for php-fpm as our version of nginx runs as nginx
#RUN sed -i -e "s/user = www-data/user = nginx/g" /etc/php5/fpm/pool.d/www.conf
#RUN sed -i -e "s/group = www-data/group = nginx/g" /etc/php5/fpm/pool.d/www.conf
#RUN sed -i -e "s/listen.owner = www-data/listen.owner = nginx/g" /etc/php5/fpm/pool.d/www.conf
#RUN sed -i -e "s/listen.group = www-data/listen.group = nginx/g" /etc/php5/fpm/pool.d/www.conf
#RUN sed -i -e "s/;listen.mode = 0660/listen.mode = 0750/g" /etc/php5/fpm/pool.d/www.conf
#RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;

RUN sed -e 's/.*listen =.*/listen = 0.0.0.0:9000/' -e 's/.*catch_workers_output.*/catch_workers_output = yes/' -i /etc/php5/fpm/pool.d/www.conf
RUN echo "php_admin_value[error_log] = stdout\nphp_admin_flag[log_errors] = on" >> /etc/php5/fpm/pool.d/www.conf

CMD ["/usr/sbin/php5-fpm", "-F"]

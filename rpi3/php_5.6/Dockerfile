# ==========================================
# Raspberry Pi 3 PHP5 Dockerfile
# ==========================================

#
# Cleaned Dockerfile
# Optimized to only apt-update and clean only once
#

# Import baseline image
#
FROM pvln/rpi3-lvl1-baseline:1.0

LABEL version="1.0" \
      maintainer="Pierre Veelen <pierre@pvln.nl>"

# ==========================================
# START OF INSTALLING APACHE2
# ==========================================
#
# Inspiration: https://writing.pupius.co.uk/apache-and-php-on-docker-44faef716150
#              https://github.com/jacksoncage/apache-docker
#

# Install apache2
#

# Important to update the packages list before installing since they are removed after the install
RUN sudo apt-get update && \
    sudo apt-get upgrade && \
    sudo apt-get install -y \
    apache2 

# Set up the minimum default apache environment variables
#
ENV APACHE_RUN_USER=www-data \
    APACHE_RUN_GROUP=www-data \
    APACHE_LOG_DIR=/var/log/apache2 \
    APACHE_LOCK_DIR=/var/lock/apache2 \
    APACHE_PID_FILE=/var/run/apache2.pid

# Set up the additional apache environment variables
#
ENV APACHE_SERVERNAME=localhost \
    APACHE_SERVERALIAS=www.localhost \
    APACHE_SERVERADMIN=pierre@pvln.nl 

# Enable apache mods.
RUN a2enmod rewrite

# Update the default apache site with the config we created.
#
ADD ./conf/apache2-config.conf /etc/apache2/sites-enabled/000-default.conf

# Change folder to sitename -> change  var/www/site to var/www/html
# sed -i "s/TextFrom/TextTo/" inWhichFile
# \/ is used to escape the / in the file path
#
RUN sed -i "s/var\/www\/site/var\/www\/html/" /etc/apache2/sites-enabled/000-default.conf

# set ownership of files
#
RUN chown -Rf ${APACHE_RUN_USER}:${APACHE_RUN_GROUP} /var/www/html

# ==========================================
#
#
# TODO CHANGE WEBSITE SERVERNAME TO PREVENT WARNING
#
# ============================================

# ==========================================
# END OF INSTALLING APACHE2
# ==========================================

# ==========================================
# START OF INSTALLING PHP5
# ==========================================

# Install php5 and cleanup afterwards
#
RUN sudo apt-get install -y \
	 libapache2-mod-php5 \
	 php5 \
	 php-pear \
	 php5-xcache \
	 php5-mysql \
	 php5-curl \
	 php5-gd && \
   sudo apt-get clean && \
   rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Update the PHP.ini file, enable <? ?> tags and quieten logging.
#
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php5/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php5/apache2/php.ini

# Enable apache mods for PHP.
#
RUN a2enmod php5

# ==========================================
# END OF INSTALLING PHP5
# ==========================================


# ==========================================
# START OF PORTS AND VOLUMES
# ==========================================

# Expose apache2 on port 80 of the container
#
EXPOSE 80

# Mountable datafolder path(s) within the container
#
# A key point to be aware of here is that anything after the VOLUME instruction
# in a Dockerfile will not be able to make changes to that volume
#
VOLUME /var/www/html
VOLUME /var/log/apache2

# ==========================================
# END OF PORTS AND VOLUMES
# ==========================================

# ==========================================
# ENTRYPOINT & CMD
# ==========================================

#
# Inspiration: http://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/
#
# Prefer ENTRYPOINT to CMD when building executable Docker image and you need a command always to be executed.
# Additionally use CMD if you need to provide extra default arguments that could be overwritten from command line when docker container runs.
# Choose CMD if you need to provide a default command and/or arguments that can be overwritten from command line when docker container runs.
#

# Cancel pre-defined start-up instruction and allow us to use our own.
#
ENTRYPOINT []

ADD ./entrypoint.sh /usr/local/bin/
ENTRYPOINT ["/bin/bash","entrypoint.sh"]

# Don't need any specific arguments to be fed into entrypoint 
CMD []

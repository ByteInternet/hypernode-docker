FROM docker.hypernode.com/byteinternet/hypernode-buster-docker-php81-mysql80:latest
MAINTAINER heesbeen

# Install + Setup Xdebug
RUN apt-get --allow-releaseinfo-change update && apt-get install -y php8.1-xdebug
#RUN echo 'zend_extension=xdebug.so\n' >> /etc/php/8.1/mods-available/xdebug.ini
RUN echo 'xdebug.mode=debug\n' >> /etc/php/8.1/mods-available/xdebug.ini
RUN echo 'xdebug.client_port="9005"' >> /etc/php/8.1/mods-available/xdebug.ini
RUN echo 'xdebug.client_host="127.0.0.1"' >> /etc/php/8.1/mods-available/xdebug.ini
RUN echo 'xdebug.max_nesting_level=-1' >> /etc/php/8.1/mods-available/xdebug.ini
RUN echo 'xdebug.idekey=PHPSTORM' >> /etc/php/8.1/mods-available/xdebug.ini

# Expose Elastic
RUN echo 'network.host: 0.0.0.0\n' >> /etc/elasticsearch/elasticsearch.yml
RUN echo 'discovery.type: single-node\n' >> /etc/elasticsearch/elasticsearch.yml

# Disable ratelimit
RUN echo "geo \$conn_limit_map { default ''; }" > /data/web/nginx/http.conn_ratelimit;/usr/bin/nginx_config_reloader

# Disable IonCube Loader
RUN rm /etc/php/*/fpm/conf.d/01-ioncube.ini
RUN rm /etc/php/*/cli/conf.d/01-ioncube.ini

# Magento 2 Support
RUN touch /data/web/nginx/magento2.flag
RUN mkdir /data/web/magento2
RUN rm -rf /data/web/public
RUN ln -s /data/web/magento2/pub /data/web/public
RUN chown -R app:app /data/web

# Magerun2 Latest Version
RUN wget https://files.magerun.net/n98-magerun2.phar
RUN chmod +x ./n98-magerun2.phar
RUN cp ./n98-magerun2.phar /usr/local/bin/n98-magerun2-latest.phar
RUN ln -sf /usr/local/bin/n98-magerun2-latest.phar /usr/local/bin/magerun2

# Install Composer Global packages
RUN composer global require mage2tv/magento-cache-clean

# Configuring Nginx
RUN wget https://raw.githubusercontent.com/heesbeen/hypernode-docker-develop/master/Assets/https.dummytag.hypernode.io.conf
RUN mv https.dummytag.hypernode.io.conf /etc/nginx/sites/https.dummytag.hypernode.io.conf

# Install Bash Profile additions
RUN echo "alias cf2='~/.config/composer/vendor/bin/cache-clean.js -d /data/web/magento2 -w'" >> /data/web/.bash_aliases
RUN echo "alias phpx='XDEBUG_CONFIG=\"idekey=PHPSTORM\" php  -dxdebug.remote_enable=on -f'" >> /data/web/.bash_aliases
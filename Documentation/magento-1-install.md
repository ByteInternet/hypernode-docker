# Installing Magento 1

Make sure you have a hosts entry set up:
```bash
# Note that you need root to write to /etc/hosts
grep -q hypernode.hypernode.local /etc/hosts || echo "172.17.0.2 hypernode.hypernode.local" >> /etc/hosts
```

Switch to a Magento 1 compatible PHP version
```bash
# Log in as root
$ ssh root@172.17.0.2 -i keys/insecure_key
# Change the PHP service version to 7.0
sed -i 's/7\.1/7.0/g' /etc/my_init.d/60_restart_services.sh
# Update PHP symlinks
update-alternatives --set php $(which php7.0)
# Restart services
bash /etc/my_init.d/60_restart_services.sh
# Log out of the Docker
exit
```

Log in to the container as the app user:
```bash
$ ssh app@172.17.0.2 -i keys/insecure_key
```

Remove any Magento 2 specific NGINX configuration:
```bash
rm -rf /data/web/nginx/magento2.flag
```

Create a new database:
```bash
echo 'create database magento;' | mysql
```

Install Magento with [n98-magerun](https://github.com/netz98/n98-magerun):
```bash
# Write the n98-magerun config file:
cat << EOF > /data/web/.n98-magerun.yaml
commands:
  N98\Magento\Command\Installer\InstallCommand:
    installation:
      defaults:
        currency: EUR
        locale: nl_NL
        timezone: UTC
        use_secure: no
        use_rewrites: yes
        session_save: files
        admin_username: admin
        admin_firstname: Firstname
        admin_lastname: Lastname
        admin_password: thisisanexamplePassword123456
        admin_frontname: example
        admin_email: example@example.com
        encryption_key:
        installation_folder: /data/web/public

    magento-packages:
      - name: byte-mag-mirror-latest
        version: 1.9
        dist:
          url: http://magento.mirror.hypernode.com/releases/magento-latest.tar.gz
          type: tar
        extra:
          sample-data: sample-data-1.9.1.0
EOF

# Install Magento 1
php -d memory_limit=1024M  /usr/local/bin/n98-magerun install --magentoVersionByName=byte-mag-mirror-latest --dbHost=127.0.0.1 --dbUser=app --dbPass=$(cat /data/web/.my.cnf  | grep pass | awk '{print$3}') --dbName=magento --installSampleData=no --useDefaultConfigParams=yes --installationFolder=/data/web/public --baseUrl=http://hypernode.hypernode.local --replaceHtaccessFile=no --no-interaction
```

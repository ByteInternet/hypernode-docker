# Switching PHP versions

Unlike the [hypernode-vagrant](https://github.com/ByteInternet/hypernode-vagrant) you can not just run `hypernode-switch-php` to change the PHP version. Because there is no systemctl to manage the services other steps must be performed. To switch to a different PHP version run the following commands:

```bash
# Edit /etc/my_init.d/restart_services.sh to use the correct PHP
vim /etc/my_init.d/restart_services.sh
# Change the enabled PHP
update-alternatives --set php $(which php7.1)
# Restart (all) services
bash /etc/my_init.d/restart_services.sh
``` 

# Switching PHP versions

Unlike the [hypernode-vagrant](https://github.com/ByteInternet/hypernode-vagrant) you can not just run `hypernode-switch-php` to change the PHP version. Because there is no systemctl to manage the services other steps must be performed. To switch to a different PHP version run the following commands:

```bash
# Change the version in magweb.json
jq ".php.version = 7.3" /etc/hypernode/magweb.json > /tmp/magweb.json
mv /tmp/magweb.json /etc/hypernode/magweb.json

# Restart all services
bash /etc/my_init.d/60_restart_services.sh
``` 

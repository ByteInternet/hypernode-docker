# Hypernode Docker Develop

## Quick start

### Pull and run container

```
cd examples/linux_development
docker build -t hypernode-docker-develop .
docker run -d -p 3306:3306 -p 22:22 -p 80:80 -p 443:443 -p 8025:8025 -p 9200:9200 -v <local_path>:/data/web/magento --name hypernode-docker-develop
```

### Copy ssh key to container

```
docker cp ~/.ssh/id_rsa.pub hypernode-docker-develop:/data/web/.ssh/id_rsa_client.pub 
docker exec hypernode-docker-develop bash -c 'cat /data/web/.ssh/id_rsa_client.pub > /data/web/.ssh/authorized_keys'
docker exec hypernode-docker-develop bash -c 'cat /data/web/.ssh/id_rsa_client.pub > /root/.ssh/authorized_keys'
```

### Disable password login

```
docker exec hypernode-docker-develop bash -c "'s/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config"
```

### Ssh into container

```
ssh app@127.0.0.1 -p22
```

### Xdebug ssh tunnel

```
ssh -oStrictHostKeyChecking=no -oUserKnownHostsFile=/dev/null app@localhost' -p22 -R 127.0.0.1:9005:127.0.0.1:9000
```

### Bash aliases

MageTV cache cleaner
```
cf --watch 
```

Php cli with Xdebug
```
phpx bin/magento
```

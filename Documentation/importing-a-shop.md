# Importing a Magento shop

1. Add a hosts entry for the Docker
```
grep -q hypernode.hypernode.local /etc/hosts || echo "172.17.0.2 hypernode.hypernode.local" >> /etc/hosts
```

1. Start an SSH agent and add your key
```
eval $(ssh-agent -s)
ssh-add  # Add your keys
```

1. Log in to the hypernode-docker container
```
# Log in with SSH agent forwarding
# The default password is 'insecure_docker_ssh_password', or use the insecure key
ssh -A root@172.17.0.2
```

1. Double-check you are in the Docker environment and not the real Hypernode
```
# This file does not exist on real Hypernodes
app@e4b7d958e69c:~$ ls /etc/hypernode/is_docker 
/etc/hypernode/is_docker
```

1. Run the [hypernode-importer](https://support.hypernode.com/knowledgebase/migrating-your-magento-to-hypernode/#Option_2_Migrate_your_shop_via_Shell_using_hypernode-importer_8211_Magento_1_2) to import a shop from a real Hypernode
```
hypernode-importer --host vdloo.hypernode.io --path /data/web/public --set-default-url
```

1. Point your browser to the importer shop

Go to http://hypernode.hypernode.local/


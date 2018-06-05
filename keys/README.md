# Keys

This directory contains a pregenerated insecure public/private keypair that can be used to log in to the `hypernode-docker` container. Do NOT use this key for production purposes. The Docker image is configured to accept this key, but there is also a default password for the `app` and the `root` user.

## Logging in with the insecure key

```
ssh -i keys/insecure_key app@172.17.0.2
```

## Logging in with the default password

```
ssh app@172.17.0.2 -o PreferredAuthentications=password -o PubkeyAuthentication=no
app@172.17.0.2's password: 
# The default password is 'insecure_docker_ssh_password'
```

# Adding keys to container

To create a Docker instance of `hypernode-docker` with your own keys, create a directory with your public key in it and a Dockerfile.

```bash
$ ls
key.pub   Dockerfile
$ cat Dockerfile
FROM docker.hypernode.com/byteinternet/hypernode-docker:latest
MAINTAINER yourname <example@example.nl>

ADD key.pub /tmp/key.pub
RUN cat /tmp/key.pub > /root/.ssh/authorized_keys && cat /tmp/key.pub > /data/web/.ssh/authorized_keys && && rm -f /tmp/deployment.pub
```

Then build the Docker with:
```bash
docker build -t hypernode-with-keys .
```

And once finished, start the Docker with:
```
docker run hypernode-with-keys
```

Note: remember that you will also need to change the default `app` and `root` user passwords if you're looking to create a secure environment.

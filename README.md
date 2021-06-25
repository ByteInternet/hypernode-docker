# hypernode-docker

The official [Hypernode](http://hypernode.com/) Docker image for Magento development. This image can be used to set up a fast and easy local development environment for Hypernode, or as a build machine in a CI environment representative of the production environment. The image contains the exact same packages and configuration as a real Hypernode except for some Docker specific tweaks. By testing and developing against this image you can be sure that when you deploy to a Hypernode in production there will be no surprises because of different software versions or configurations.

We build this image multiple times a day (every time we do a [release](https://support.hypernode.com/category/changelog/)) by applying our configuration management on the [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker) ['fat' container](https://blog.phusion.nl/2015/01/20/baseimage-docker-fat-containers-treating-containers-vms/). By treating the Docker as a lightweight VM instead of as a vehicle for a single process we stay close to what an actual Hypernode actually looks like. No micro-services or a multi-container application, but a single instance with minimal network overhead and all batteries included.

The `hypernode-docker` image has SSH, PHP, NGINX, MySQL, Redis and Varnish. The biggest difference between a real Hypernode and this container is that this environment does not have an [init system](https://en.wikipedia.org/wiki/Init). While it is possible to [run systemd within a Docker Container](https://developers.redhat.com/blog/2014/05/05/running-systemd-within-docker-container/) if the host is also runs [systemd](https://www.freedesktop.org/wiki/Software/systemd/), we choose not to do so to achieve greater compatibility and user-friendliness.

## Usage

### The Debian Buster hypernode-docker

Starting the container
```
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker:latest
docker run docker.hypernode.com/byteinternet/hypernode-buster-docker:latest
```

Now with the introduction of Debian Buster we have also made it so that for some of the possible configurations we have provided a pre-built image so it is not necessary anymore to [manually install a different MySQL](https://github.com/ByteInternet/hypernode-docker/issues/33) for example than what would be in the default box.

These are the URLs of the containers with specific configurations:
```
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php56-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php70-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php71-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php72-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php73-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php74-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php80-mysql56:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php56-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php70-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php71-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php72-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php73-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php74-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php80-mysql57:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php56-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php70-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php71-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php72-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php73-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php74-mysql80:latest
docker pull docker.hypernode.com/byteinternet/hypernode-buster-docker-php80-mysql80:latest
```

### Logging into the container

Get the IP address
```bash
# Find the container ID
docker ps
# Find the IP address of the container
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <the container ID>
```

Log in to the container:
```bash
# SSH into the machine using the retrieved IP address. Example:
ssh -oStrictHostKeyChecking=no -oUserKnownHostsFile=/dev/null app@172.17.0.2
# Or as the root user
ssh -oStrictHostKeyChecking=no -oUserKnownHostsFile=/dev/null root@172.17.0.2
```

The password is `insecure_docker_ssh_password`, or use the [pregenerated key](keys/README.md).

### Restarting services

Because `systemctl` is not available inside the container, the services are started by an [init script](https://github.com/phusion/baseimage-docker#running-scripts-during-container-startup) on container startup. Note that because the systemd unit files are not parsed there could be a slight drift between what commandline flags the processes actually use in production compared to in this contanier. 

To restart all services run:

```bash
bash /etc/my_init.d/60_restart_services.sh
```

If you only want to restart a specific service, inspect the `restart_services.sh` script and kill the screen for the service you want to restart. Then run the corresponding screen command to start the service again.

## See our excellent Documentation!

[Starting the container](Documentation/starting-the-container.md)

[Importing an existing Magento shop into the Docker](Documentation/importing-a-shop.md)

[Switching PHP versions](Documentation/switching-php-versions.md)

[Debugging with Xdebug in PhpStorm](Documentation/debugging-with-xdebug-in-phpstorm.md)

[Adding your own keys to the container](Documentation/adding-keys-to-container.md)

[Inspecting emails sent from the Docker](Documentation/inspecting-emails.md)

[Installing Magento 1](Documentation/magento-1-install.md)

[Mac specific example](examples/osx_development/README.md)

## Inspiration

If you're wondering about how you can make best use of this image, check out some of these awesome project(s) by other users.

### [Disposable Magento testing environments with Kubernetes](https://elgentos.nl/blog/disposable-magento-testing-environments-with-k8s/)

[Peter Jaap Blaakmeer](https://github.com/peterjaap) explains how [Elgentos](https://elgentos.nl) automatically sets up disposable testing environments for branches, tags and commits in their repositories by deploying the `hypernode-docker` image on a Kubernetes cluster on the Google Cloud Platform.

### [Local development with the Hypernode Docker container](https://blog.guapa.nl/local-development-with-the-hypernode-docker-container-linux?)

[Ruthger Idema](https://github.com/ruthgeridema) describes how [Guapa](https://www.guapa.nl/) mimics their production environments as close as possible when developing on their local machines. Also includes a nice example for setting up Xdebug to work with PHPstorm in the `hypernode-docker`.

## Related projects

### [hypernode-vagrant](https://github.com/ByteInternet/hypernode-vagrant)

A similar local development or build environment based on [Vagrant](https://github.com/ByteInternet/hypernode-vagrant), using [Virtualbox](https://www.virtualbox.org/) VMs or [LXC](https://linuxcontainers.org/) containers. The difference between hypernode-docker and hypernode-vagrant is that the Vagrant has a complete init system, making it more like a real Hypernode, while the Docker could be more user-friendly depending on your environment and preferences.

### [hypernode-deployment-hackathon](https://github.com/Hypernode/hypernode-deployment-hackathon)

A work in progress Magento 2 module built by the community that can be used as a building block in combination with the Docker container in this repository to simplify the testing, building and deployment steps in your CI pipeline.


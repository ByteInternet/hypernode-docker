# hypernode-docker

The official [Hypernode](http://hypernode.com/) Docker image for Magento development. This image can be used to set up a fast and easy local development environment for Hypernode, or as a build machine in a CI environment representative of the production environment. The image contains the exact same packages and configuration as a real Hypernode except for some Docker specific tweaks. By testing and developing against this image you can be sure that when you deploy to a Hypernode in production there will be no surprises because of different software versions or configurations.

We build this image multiple times a day (every time we do a [release](https://support.hypernode.com/category/changelog/)) by applying our configuration management on the [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker) ['fat' container](https://blog.phusion.nl/2015/01/20/baseimage-docker-fat-containers-treating-containers-vms/). By treating the Docker as a lightweight VM instead of as a vehicle for a single process we stay close to what an actual Hypernode actually looks like. No micro-services or a multi-container application, but a single instance with minimal network overhead and all batteries included.

The `hypernode-docker` image has SSH, PHP, NGINX, MySQL, Redis and Varnish. The biggest difference between a real Hypernode and this container is that this environment does not have an [init system](https://en.wikipedia.org/wiki/Init). While it is possible to [run systemd within a Docker Container](https://developers.redhat.com/blog/2014/05/05/running-systemd-within-docker-container/) if the host is also runs [systemd](https://www.freedesktop.org/wiki/Software/systemd/), we choose not to do so to achieve greater compatibility and user-friendliness.

## Usage

### Starting the Docker and logging in

Starting the container
```bash
docker pull docker.hypernode.com/byteinternet/hypernode-docker:latest
docker run docker.hypernode.com/byteinternet/hypernode-docker:latest
```

Get the IP address
```bash
# Find the container ID
docker ps <docker name>
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

[Adding your own keys to the container](Documentation/adding-keys-to-container.md)

[Inspecting emails sent from the Docker](Documentation/inspecting-emails.md)

[Installing Magento 1](Documentation/magento-1-install.md)

[Mac specific example](examples/osx_development/README.md)

## Related projects

### [hypernode-vagrant](https://github.com/ByteInternet/hypernode-vagrant)

A similar local development or build environment based on [Vagrant](https://github.com/ByteInternet/hypernode-vagrant), using [Virtualbox](https://www.virtualbox.org/) VMs or [LXC](https://linuxcontainers.org/) containers. The difference between hypernode-docker and hypernode-vagrant is that the Vagrant has a complete init system, making it more like a real Hypernode, while the Docker could be more user-friendly depending on your environment and preferences.

### [hypernode-deployment-hackathon](https://github.com/Hypernode/hypernode-deployment-hackathon)

A work in progress Magento 2 module built by the community that can be used as a building block in combination with the Docker container in this repository to simplify the testing, building and deployment steps in your CI pipeline.


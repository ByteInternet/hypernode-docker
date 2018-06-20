Use Hypernode-docker as a development environment on OSX
========================================================

Docker for Mac has some networking and filesystem limitations as opposed to running Docker on a Linux environment. 
This example shows how you could overcome those limitations and use `hypernode-docker` as a practical development environment on Mac OSX.
After setting this up, your application will be available in a dockerized hypernode environment while still being able to use your IDE's development capability's like code intelligence.

This __minimal example__ follows the same practices used within [hypernode-docker-osx](https://github.com/frosit/hypernode-docker-osx) development setup but without all the additional scripting and configurations. 


# 1. Requirements & Installation

We used [Docker for mac](https://docs.docker.com/docker-for-mac/) for this setup. You should have that ready before proceeding.

In addition to the `hypernode-docker` container we used the following to overcome the limitations:

* [Docker-sync](http://docker-sync.io/) (Improves disk performance when mounted / synced)
* [docker-compose](https://docs.docker.com/compose/compose-file/compose-file-v2/) (for additional configurations, should be part of Docker for mac)

__Installing docker-sync__

```bash
# If you have 'gem' available
gem install docker-sync

# Else, if you have 'brew' available
# https://github.com/EugenMayer/docker-sync/wiki/docker-sync-on-OSX
brew install unison
brew install eugenmayer/dockersync/unox
```

Besides unison, there are more sync strategies available.

# 2. Configure your environment

We need the following files to set things up:

* docker-compose.yml
* docker-sync.yml

Copy those files to your project for editing (preferably one directory above your source code)


## 2.1 Minimal Configuration

The minimal setup requires you to specify the path to your source code and optionally change ports if they are not available.

__Specify the path to your projects' source code__

To have our project synced between the folder in our IDE and `hypernode-docker`, we have to define the path to the folder we want to sync in 'src', and where we want to sync it to in our hypernode (magento2/public)

```yaml
# docker-sync.yml
syncs:
  myproject-app-sync:
    src: './magento' # <<<----- Path to local directory
    
# docker-compose.yml
    volumes:
      - app-sync:/data/web/public:nocopy # <<<----- Path to directory in hypernode-docker (currently: /data/web/public)
```

Magento 2 example

```yaml
# docker-sync.yml
syncs:
  myproject-app-sync:
    src: './magento' # Local directory
    
# docker-compose.yml
    volumes:
      - app-sync:/data/web/magento2:nocopy # <<<---- /data/web/magento2 - create symlinks to public later
```

You could also specify an empty directory in 'src' and have the `hypernode-importer` import the store afterwards.

__Handle port conflicts (Optional: if port 80/3306 is not available on your mac)__

The hypernode container runs on localhost, if you have a local web server like MAMP on port 80, docker won't start because of a port conflict. Change the conflicting ports to something available in `docker-compose.yml.

```yaml
    # docker-compose.yml
    ports:
      - '2222:22' # Connect with: ssh app@127.0.0.1 -p 2222
      - '8080:80' # Maps port 80 to 8080 -> http://localhost:8080
      - '3307:3306'
```

You could also just remove the 3306 port mapping and use SSH tunneling with [Sequel Pro](https://www.sequelpro.com/) instead if you like a MySQL GUI.


## 2.2 Advanced configuration (optional)

You can go to great lengths with a setup like this, In addition to the minimal configurations, you could configure things even further.

__Multiple projects__

Be aware of port/name conflicts when setting up multiple projects, Use the project name as a prefix. (example: coolstore.shop)

```yaml
# docker-sync.yml
syncs:
  coolstore-app-sync: # Prefixed with project name, matches volume name (must be unique)
    src: './magento'


# docker-compose.yml
services:
  hypernode-docker:
    container_name: coolstore-docker # Use the same project prefix to keep things organised
    ...
    volumes:
      - app-sync:/data/web/public:nocopy
    ...    
volumes:
  app-sync:
    external:
      name: 'coolstore-app-sync' # Matches name in docker-sync.yml
```

__Add development domains to your hosts file__

If you want your hypernode-docker instance to respond to something more descriptive than localhost, You should edit your `/etc/hosts` file with for example: `127.0.0.1    myproject-mydomain.tld`

```bash
sudo nano /etc/hosts
```
``` 
# /etc/hosts
255.255.255.255 broadcasthost
::1             localhost
127.0.0.1       myproject-hyperdocker.io  # <=== our development domain
```

__Configure your volume sync__

`docker-sync` doesn't have to keep track of all changes that happen in your Magento installation, only the things that matter. With all the caching, compiling and logging Magento does these days docker-sync might be rather busy
and it could be wise to define what should be watched and synced, and what not.

If it's ignored by git, you could exclude it. There are things you maybe don't wan't to exclude like your media/wysiwyg folder if your working with images. Below is an example of how a sync could be configured.

```yaml
# docker-sync.yml
syncs:
  magento2-app-sync:
    src: './magento'
    sync_excludes: ['vendor', 'var','pub/media/catalog/product/cache','pub/static','pub/media/tmp','dev','generated']
    watch_excludes: ['vendor', 'var','pub/media/catalog/product/cache','pub/static','pub/media/tmp','dev','generated','lib','setup','update','.*/.git', '.*/node_modules'] # Only changes in app and media
    sync_userid: '1001'
    notify_terminal: true
    monit_enable: true
    monit_interval: 10
    monit_high_cpu_cycles: 6
```

For more configuration options, see [Docker-sync configurations](https://github.com/EugenMayer/docker-sync/wiki/2.-Configuration)


# 3. Start your environment

When all is configured, It's time to boot our environment.

__1. docker-sync start__

We have start `docker-sync` first for our volumes to become available. Head to the directory that contains `docker-sync.yml` and run:

```bash
# Starting docker-sync
# Make sure the directory in 'src' exists or it wil error!
docker-sync start
```

If the folder you specified in `src` is empty, it only takes a few seconds. If it's a full Magento installation, the initial sync could take up to a few minutes based on the size. __Wait for it to finish__, otherwise docker can't find the volume and won't start.
You know it's ready when you see something similar to below.

```yaml
#         ok  starting initial sync of myproject-app-sync
#    success  Sync container started
#    success  Starting Docker-Sync in the background
```

__2. docker-compose up__

Open a new terminal / screen session, head to your directory containing the `docker-compose.yml` and run:

```bash
docker-compose up
```

When it's done you will see:

```
myproject-hypernode-docker | *** Booting runit daemon...
myproject-hypernode-docker | *** Runit started as PID 272
```

Your environment should now be available on the ports you specified. 

__3. Login with SSH__

To login over SSH, use the configured port together with the `-p` parameter, for example:

```bash
# Default password for app/root: insecure_docker_ssh_password
ssh -A app@127.0.0.1 -p 2222
```

__(important) Before you shutdown__

Shutting down means losing everything that is not in a synced place. For example imported databases, modified nginx files, additional dotfiles etc.
Ensure to export your database before shutting down, this allows you to easily import it when you boot it back up. To deal with the loss of these kind of things, you could add extra volume mounts.

The example below adds 2 more mounts, bin (provisioning/ configuration scripts), shared (storing backups, dumps and others). the volumes in the example below don't use docker-sync as they won't be heavily used.

```yaml
# docker-compose.yml
    ...
    volumes:
      - './shared:/data/web/shared' # For database dumps, backups, config files, etc
      - './bin:/data/web/bin' # For scripts to help you configure, prepare for shutdown, etc.
      - app-sync:/data/web/public:nocopy # docker-sync mount
```

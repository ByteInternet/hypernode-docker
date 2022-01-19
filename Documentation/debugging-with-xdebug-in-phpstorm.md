Debugging with Xdebug in PhpStorm
=================================

[Xdebug](https://xdebug.org/) is a PHP debugging tool. You can use `php-xdebug` in the `hypernode-docker` to debug the PHP code of your shop. This is a step by step guide to set up the debugger.


## Get PhpStorm

If you don't already have PhpStorm, you can go get it [here](https://www.jetbrains.com/phpstorm/).


## Get a hypernode-docker up and running

To start up the `hypernode-docker` you can do this:
```
docker pull docker.hypernode.com/byteinternet/hypernode-docker:latest
docker run docker.hypernode.com/byteinternet/hypernode-docker:latest
```

Keep this open in a different shell and note the IP address.


## Set up php-xdebug in the docker

In another shell, log in to the docker as root.
```
# The password should be in the output from the docker run command
ssh root@172.17.0.2
```

Inside the docker, install php-xdebug. 
```
apt-get install php-xdebug
```

Or if you are running PHP 8.0 you need to install:
```
apt-get install php8.0-xdebug
```

And for PHP 8.1:
```
apt-get install php8.1-xdebug
```

Note: if you're doing this manually all the time consider making a Dockerfile based on `hypernode-docker` that does this as part of the image.

Enable `php-xdebug` for `php-fpm`
```
# This file should exist and contain only the line: zend_extension=xdebug.so
vim /etc/php/7.1/fpm/conf.d/20-xdebug.ini
# In this file put these extra lines:
xdebug.remote_enable=1
xdebug.remote_port="9005"
xdebug.remote_host="127.0.0.1"
```

Next, restart the services with `bash /etc/my_init.d/60_restart_services.sh`

If you also want to enable xdebug for cli see the `/etc/php/<phpversion>/cli` dir


## Mount the remote application filesystem

In order to set breakpoints PhpStorm needs to have access to the filesystem of the Docker container. You can set this up in many different ways (like Docker volumes). In this example sshfs is used.

Make sure [sshfs installed and configured](https://www.linode.com/docs/networking/ssh/using-sshfs-on-linux/):
```
# On Ubuntu / Debian as root
apt-get install sshfs
groupadd fuse || /bin/true
usermod -a -G fuse <youruserthatisntroot>
```

Create the mount directory:
```
mkdir -p /mnt/sshfs
```

Mount the webroot of the docker as the `app` user:
```
# This has the same password as root
sshfs -o allow_other app@172.17.0.2:/data/web/public /mnt/sshfs
```

Note that in case of Magento 2 and Shopware 6 you might want to mount `/data/web/magento2` instead for example as that contains the web root. In Magento 1 the webroot generally is the whole application.

Now if you go to `/mnt/sshfs` on your computer you should be able to see and edit the files that are served by the Docker on `172.17.0.2`.

If you have no application you can create a PHP file like so for testing:
```
$ cat index.php 
<? phpinfo();
```

## Install a Xdebug browser plugin

You need this to tell the webserver to start the debugging process later. [This browser extension for chrome](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc) can be used. For more info see [this page by jetbrains on browser debugging extensions](https://www.jetbrains.com/help/phpstorm/browser-debugging-extensions.html).

## Configure PhpStorm for Xdebug

Open your `/mnt/sshfs` project in PhpStorm. Then, go to `File->Settings` and navigate to `Languages & Frameworks -> PHP -> Debug` and configure these settings:
```
Disabled: Ignore external connections through unregistered server configurations
Disabled: Break at first line in PHP scripts
Debug port: 9000
Enabled: Can accept external connections
Disabled: Force break at first line when no path mapping specified
Disabled: Force break at first line when a script is outside the project
Enabled: Automatically detect IDE IP: mostly the default here is OK but make sure it at least includes 127.0.0.1
```

Next, click `Run -> Start Listening For PHP Debug Connections`

On your computer a new port should now be bound. You can verify this like so:
```
# netstat -tulpn | grep 9000
tcp        0      0 0.0.0.0:9000            0.0.0.0:*               LISTEN      25434/java  
```

## Forward the PhpStorm port to the Docker

As the next step we need to forward this port that PhpStorm has opened up to the Docker so that the `php-fpm` service inside the Docker can relay the debug information to the PhpStorm IDE. In another shell do the following:

```
ssh root@172.17.0.2 -R 127.0.0.1:9005:127.0.0.1:9000
```

This will forward the TCP port 9000 on your desktop / laptop to port 9000 in the hypernode-docker container. You can again verify if this worked:
```
# The 9005 port should now be bound on 127.0.0.1 in the Docker
root@3023654a6956 ~ # netstat -tulpn | grep 9005
tcp        0      0 127.0.0.1:9005          0.0.0.0:*               LISTEN      5904/6 

# And the process doing that is our SSH connection
root@3023654a6956 ~ # ps auxf | grep 5904
postfix     1200  0.0  0.0  80704  5904 ?        S    09:55   0:00  \_ tlsmgr -l -t unix -u -c
root        5904  0.0  0.0  97088  7632 ?        Ss   11:06   0:00  |       \_ sshd: root@pts/6
root        5973  0.0  0.0  20656   932 pts/6    S+   11:07   0:00  |               \_ grep 5904
```

Keep in mind that if you close that SSH session the connection will be gone!

## Start the debug process

In your browser go to http://172.17.0.2/. If you followed the `phpinfo` example above you should see the phpinfo page displaying the version information and such.

Next, in PhpStorm go to this `index.php` and click in the gutter near the line number. This will create a red ball next to the number and light up the line. This is a breakpoint. Execution will halt here when the page is loaded and you will get a chance to look around in the PHP [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) at this point in the process.

In the browser with http://172.17.0.2/ open click the Debug extension and enable `Debug`. If you now reload the page a popup should appear in PhpStorm. After configuring the path mapping you can interact with the console.

## Other resources

Check out this link for more info about phpstorm and xdebug: https://www.jetbrains.com/help/phpstorm/configuring-xdebug.html

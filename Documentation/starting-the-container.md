# Starting the container

## Running the container in the foreground

To start the container in the foreground, run the following command:

```bash
$ docker pull docker.hypernode.com/byteinternet/hypernode-docker:latest
$ docker run docker.hypernode.com/byteinternet/hypernode-docker:latest
*** Running /etc/my_init.d/00_regen_ssh_host_keys.sh...
*** Running /etc/my_init.d/10_login_instructions.sh...
                                              _
  /\  /\_   _ _ __   ___ _ __ _ __   ___   __| | ___
 / /_/ / | | | '_ \ / _ \ '__| '_ \ / _ \ / _` |/ _ \
/ __  /| |_| | |_) |  __/ |  | | | | (_) | (_| |  __/
\/ /_/  \__, | .__/ \___|_|  |_| |_|\___/ \__,_|\___|
        |___/|_|

Host/IP:    xxxxx-dummytag-docker.nodes.hypernode.io (172.17.0.3)
Account_id: 666
Release:    release-5278-28-ga9d3844 @ 2018-06-05 12:56:34 UTC

Handy commands (more at http://support.hypernode.com):

 livefpm (see live PHP status)
 tal | pnl (follow live requests)

 hypernode-importer (import a Magento shop)
 hypernode-image-optimizer (reduce image size)
 hypernode-ftp (manage FTP accounts)

 magerun list (many useful extensions)

------------------------------------------------------------------------------
The SSH daemon will be up in a couple of seconds, you can login with: ssh -A app@172.17.0.3
*** Running /etc/my_init.d/50_copy_key.sh...
*** Running /etc/my_init.d/50_fix_mailname.sh...
*** Running /etc/my_init.d/51_postfix.sh...
 * Starting Postfix Mail Transport Agent postfix
   ...done.
*** Running /etc/my_init.d/60_restart_services.sh...
Killing old Hypernode services
Killing any old NGINX service
Killing any old Redis service
Killing any old PHP-FPM service
Killing any old MySQL service
Killing any old Mailhog service
Killing any old Varnish service
Giving any old services 5 seconds to stop..
No Sockets found in /var/run/screen/S-root.

Starting new detached hypernode services. See screen -x
Starting NGINX
Starting Redis
Starting PHP
Starting MySQL
Starting Mailhog
Starting Varnish
Giving the new services a couple of seconds to start..
hypernode-docker status: everything ok
*** Running /etc/rc.local...
*** Booting runit daemon...
*** Runit started as PID 312
```

You can now log in with SSH using the IP address seen in the output.

## Running the container in the background

To start the container in the background, run the following command:
```bash
$ docker run -d docker.hypernode.com/byteinternet/hypernode-docker:latest
36532b58822cf27f74234f6afc01db21fca15b480bf43634ae725db35047dc5a
```

The IP address of the container can then be found with:
```bash
$ docker inspect -f '{{ .NetworkSettings.IPAddress }}' 36532b58822cf27f74234f6afc01db21fca15b480bf43634ae725db35047dc5a
172.17.0.3
```

You can view the logs with `docker logs`
```bash
$ docker logs 36532b58822cf27f74234f6afc01db21fca15b480bf43634ae725db35047dc5a
*** Running /etc/my_init.d/00_regen_ssh_host_keys.sh...
*** Running /etc/my_init.d/10_login_instructions.sh...
                                              _
  /\  /\_   _ _ __   ___ _ __ _ __   ___   __| | ___
 / /_/ / | | | '_ \ / _ \ '__| '_ \ / _ \ / _` |/ _ \
/ __  /| |_| | |_) |  __/ |  | | | | (_) | (_| |  __/
\/ /_/  \__, | .__/ \___|_|  |_| |_|\___/ \__,_|\___|
        |___/|_|

Host/IP:    xxxxx-dummytag-docker.nodes.hypernode.io (172.17.0.3)
Account_id: 666
Release:    release-5278-28-ga9d3844 @ 2018-06-05 12:56:34 UTC

Handy commands (more at http://support.hypernode.com):

 livefpm (see live PHP status)
 tal | pnl (follow live requests)

 hypernode-importer (import a Magento shop)
 hypernode-image-optimizer (reduce image size)
 hypernode-ftp (manage FTP accounts)

 magerun list (many useful extensions)

------------------------------------------------------------------------------
The SSH daemon will be up in a couple of seconds, you can login with: ssh -A app@172.17.0.3
*** Running /etc/my_init.d/50_copy_key.sh...
*** Running /etc/my_init.d/50_fix_mailname.sh...
*** Running /etc/my_init.d/51_postfix.sh...
 * Starting Postfix Mail Transport Agent postfix
   ...done.
*** Running /etc/my_init.d/60_restart_services.sh...
Killing old Hypernode services
Killing any old NGINX service
Killing any old Redis service
Killing any old PHP-FPM service
Killing any old MySQL service
Killing any old Mailhog service
Killing any old Varnish service
Giving any old services 5 seconds to stop..
No Sockets found in /var/run/screen/S-root.

Starting new detached hypernode services. See screen -x
Starting NGINX
Starting Redis
Starting PHP
Starting MySQL
Starting Mailhog
Starting Varnish
Giving the new services a couple of seconds to start..
hypernode-docker status: everything ok
*** Running /etc/rc.local...
*** Booting runit daemon...
*** Runit started as PID 312
```

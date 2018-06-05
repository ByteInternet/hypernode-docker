# Inspecting emails sent from the Docker

Like in the [hypernode-vagrant](https://github.com/ByteInternet/hypernode-vagrant#mail) project, all emails are caught and redirected to a [MailHog](https://github.com/mailhog/MailHog) instance. You can visit the mailhog web interface on port `8025`. So for example, if the IP of your container is `172.17.0.2` you would be able to access MailHog on [http://172.17.0.2:8025/](http://172.17.0.2:8025/). 

Alternatively you could set up a hosts entry like so on the host (not in the Docker):
```bash
grep -q hypernode.hypernode.local /etc/hosts || echo "172.17.0.2 hypernode.hypernode.local" >> /etc/hosts
```

And then visit the MailHog instance on [http://hypernode.hypernode.local:8025](http://hypernode.hypernode.local:8025).

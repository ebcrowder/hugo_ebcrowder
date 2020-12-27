---
date: "2020-03-11"
title: "How to Setup bitwarden_rs on a Alpine Linux Virtual Machine"
tags: ["Linux", "Security", "Bitwarden"]
draft: false
---

[Previously](https://ebcrowder.dev/2019-09-20), I described how to deploy [bitwarden_rs](https://github.com/dani-garcia/bitwarden_rs) using AWS Fargate. Fargate is a great service, but is relatively expensive and a bit overkill for simple, lightweight deployment use cases.

For a cloud deployment of bitwarden_rs, we really just need a dedicated VM. Fortunately, we have a few options:

- Google Cloud Platform (GCP) offers **free** "f1-micro" VMs
- Most of the other major IAAS providers (Digital Ocean, Linode) have \$5 per month VMs that are highly configurable and are available with most Linux distributions (Debian and Ubuntu).

For more info about the Bitwarden project, see the aforementioned post.

For this post, we will be setting up bitwarden_rs using [Alpine Linux](https://www.alpinelinux.org/). Linode is a great choice for this distribution as they offer Alpine Linux images for all of their VM options. However, these instructions should port fairly easily to other Linux distros - just substitute the applicable package manager in for Alpine's `apk`.

### setup

Launch a Alpine Linux VM and update the operating system and related packages:

- `apk update`
- `apk upgrade`

### install docker

- `apk add docker`
- `service docker start` will produce the following output:

```bash
 * Caching service dependencies .. [ ok ]
 * Mounting cgroup filesystem .. [ ok ]
 * /var/log/docker.log: creating file
 * /var/log/docker.log: correcting owner
 * Starting Docker Daemon .. [ ok ]
```

### install sqlite

bitwarden_rs uses sqlite as its database. In order to interact with it, we will need to install the `sqlite` client.

- `apk add sqlite`

### get bitwarden_rs image and run

- `docker run -d --name bitwarden -v /bw-data/:/data/ -p 80:80 bitwardenrs/server:latest`
- `docker ps` will approximate the following:

```bash
CONTAINER ID IMAGE                     COMMAND
eb9df44334a0 bitwardenrs/server:latest "/bitwarden_rs"
```

- the bitwarden_rs application will be running at the IP address of your VM. However, before we can use the application in secure manner, we need to set up our VM to be accessible via HTTPS.
- stop the docker container: `docker stop bitwarden`

### add a domain

In order to use HTTPS, we need to assign our VM a domain and generate a HTTPS certificate. Each of the major cloud providers (Amazon, Google, Azure) provide simple solutions for managing the DNS of a particular VM. Digital Ocean and Linode also provide similar DNS solutions. So, pick a domain name of your choosing and set it up for your VM.

### setup https via lets encrypt

- install certbot
  `apk add certbot`
- generate a cert
  `certbot certonly --standalone`
- enter info as directed by the CLI
- set up automatic renewal via adding a cron job to the default crontab
  `echo "0 0,12 \* \* _ root python -c 'import random; import time; time.sleep(random.random() _ 3600)' && certbot renew -q" | sudo tee -a /etc/crontab > /dev/null`

### completion

- substituting in your domain, launch the bitwarden_rs container with SSL/HTTPS enabled:

```bash
sudo docker run -d --name bitwarden
-e ROCKET_TLS='{certs="/ssl/live/
YOUR_DOMAIN_HERE/fullchain.pem",
key="/ssl/live/YOUR_DOMAIN_HERE/privkey.pem"}'
-v /etc/letsencrypt/:/ssl/
-e SIGNUPS_ALLOWED=false
-e WEB_VAULT_ENABLED=false
-v /bw-data/:/data/
-p 443:80
bitwardenrs/server:latest
```

- test by accessing the URL that you assigned to the VM. You should see the Bitwarden web user interface.
- Once your user(s) have been created and set up, consider re-running the container with the following options so that the Bitwarden web user interface and new user signups are disabled:

```bash
-e SIGNUPS_ALLOWED=false
-e WEB_VAULT_ENABLED=false
```

- Lastly, you can backup your database by running the following:
  `sqlite3 db.sqlite3 ".backup 'backup.sqlite3'"`

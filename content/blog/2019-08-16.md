---
date: "2019-08-16"
title: "How to Install HTTPS Certificates on a cPanel Linux Server"
tags: ["HTTPS", "Linux", "cPanel"]
draft: false
---

I was recently tasked with helping a friend update a website deployed on a cPanel box provided via GoDaddy. As part of this update, I needed to install a HTTPS certificate via [Let's Encrypt](https://letsencrypt.org/). This took some research, but I was able to string together a solution from a few different sources. So, I thought I would document the solution in case it can help someone else, or at the very least help me remember what I changed in case I have to revert anything!

Although I was dealing with a cPanel box deployed via GoDaddy, the following should work for any cPanel box, regardless of method of deployment.

# 1 - SSH into cPanel

Depending on how the cPanel box is deployed, you will most likely have to refer to your provider's documentation for enabling SSH access to the cPanel box. Once you have this set up and are able to successfully SSH into the box, the rest is easy!

# 2 - Install Let's Encrypt

Let's Encrypt recommends users who have Shell (terminal) access to use the [Certbot](https://certbot.eff.org/) ACME client to install and manage certificates. There are a lot of available tools out there for managing HTTPS certificates issued by Let's Encrypt. After a bit of searching, I found that [acme.sh](https://github.com/Neilpang/acme.sh) was highly recommended, so we will use that do the following:

Install acme.sh:

```bash
curl https://get.acme.sh | sh
```

Then, reload your `.bashrc` file so that you have access to `acme.sh`.

# 3 - Issue HTTPS certificate

Now that you have access to `acme.sh`, let's issue a new certificate for our domain:

```bash
acme.sh --issue -d EXAMPLEDOMAIN.com -w /home/<SSHUSERNAME>/public_html
```

- substitute `EXAMPLEDOMAIN` and `SSHUSERNAME` with your respective information.

# 4 - Deploy the certificate

Now, let's deploy the certificate:

```bash
acme.sh --deploy -d EXAMPLEDOMAIN.com --deploy-hook cpanel_uapi
```

# 5 - Configure cPanel server to redirect traffic to HTTPS

Now that we have successfully installed the HTTPS certificate, we need to redirect all traffic that accesses our URL to utilize HTTPS.

Your cPanel server should have a `.htaccess` file in the `/public_html` directory. Open this file using `vim` to edit it via:

```bash
vim .htaccess
```

Then, insert the following:

```bash
#RewriteEngine on

#Comment | Force all URLs to https WITHOUT www
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://example.com/$1 [R,L]

#Comment | Force all URLs to https WITH www
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://www.example.com/$1 [R,L]
```

These rules will route all traffic to HTTPS regardless of whether they access your site via `www` or not. If your use case requires other behavior, disable the applicable rules by commenting them out or deleting them.

Congratulations! Enjoy your free HTTPS certificate!

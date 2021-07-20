---
title: Reverse proxy for dummies with Nginx Proxy manager
toc: true
toc_label: "Table of Contents"
tags:
  - thoughts
  - en
categories:
  - guides
---

[There was a time](2017-02-07-letsencrypt-nginx.md) when setting up reverse proxy with letsencrypt wasn't as easy. But today it's simply matter of few commands and 2-3 button clicks in beautiful UI. 

## Prerequisites and preparations

Requirements: 
- server or VPS. Anything that can run docker and has a network will suffice;
- registered domain name;
- time, because time is precious.

It is assumed our soon-to-be proxy server has Ubuntu 20.04 as OS, prepared and secured according to following guidelines:

- `update && upgrade`;
- SSH-keys for login (follow [this guide](https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/create-with-putty/, if your are on Windows));
- [Initial Ubuntu setup](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04). I use [Digital Ocean](https://docs.digitalocean.com/products/networking/firewalls/) firewall instead of ufw, since it's easy to use and filters traffic even before it reaches protected droplet. And you can apply all rules to all droplets at once.

With Digital Ocean you can automate some initial stuff with *User data* script during dropplet creation. DO [uses](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup) YAML-based scripting language, so mind spaces and indents. Example scripts from random github: [@mjradwin](https://gist.github.com/mjradwin/9e545681a4cc8aba2f19784a97a144f4), [@c0psrul3](https://gist.github.com/c0psrul3/f2627de45f7d244afa48b0fe191a9ece).

Add your non-root user (replace poor `sammy`):

```
adduser sammy
password: 
usermod -aG sudo sammy
rsync --archive --chown=sammy:sammy ~/.ssh /home/sammy
```

Change SSH port and turn off root ssh login (`nano /etc/ssh/sshd_config` and `sudo service sshd restart`). Remember, you can always restore access via droplet management UI.

Follow guide to install [docker](https://docs.docker.com/engine/install/ubuntu/) and [docker-compose](https://docs.docker.com/compose/install/). Do not forget add your non-privileged user to *docker* group: `sudo usermod -aG docker sammy` ([Docker Linux postinstall guide](https://docs.docker.com/engine/install/linux-postinstall/)).

Tips: 
- select additional option *Monitoring* during creation, if you don't plan using self-hosted monitoring solution;
- you can also use [community ansible scripts](https://github.com/do-community/ansible-playbooks) to automate initial setup.

## Reverse proxy

Reverse proxy can be used to direct and secure traffic for all hosts behind proxy. Easiest way to setup and manage reverse proxy is [Nginx Proxy Manager](https://nginxproxymanager.com/) ([Source](https://github.com/jc21/nginx-proxy-manager)).

Setup is dead easy: [install guide](https://github.com/jc21/nginx-proxy-manager#quick-setup). Just copy provided `.yml` content and run `docker-compose up -d`.
Login to admin page and change password. 

First proxy to create is for NPM itself:

1. In your domain manager create A-record pointing to the NPM server.

![image-20210701150733756](/assets/images/posts/2021-05-20-rp1.png)

2. In NPM create Proxy Host (**Hosts** > **Proxy host**). On **Detail** tab set your FQDN and point it your NPM to *http 127.0.0.1 port 81*. Check **Block common exploits**, because why not. 
3. On SSL tab accept ToS. Check Froze SSL, HSTS Enabled, HTTP/2 Support. Issue the certificate.

![do-npm-02](/assets/images/posts/2021-05-20-rp2.png)

4. Ensure that your admin page is available by FQDN.
5. Edit `docker-compose.yml` to remove `81:81` line in the *ports* section and run `docker-compose up -d`
6. First login after reload can take 1-2 mins, don't worry, everything will be ok!
7. Now we have our NPM admin page safe and secured with SSL.

